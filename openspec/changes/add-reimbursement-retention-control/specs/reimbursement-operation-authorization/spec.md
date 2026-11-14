## ADDED Requirements

### Requirement: Protected reimbursement operations are authorized before execution
The system SHALL classify every billing, claim, remittance, and payer-disclosure attempt as a protected operation and SHALL authorize it before any protected side effect. The decision SHALL evaluate current actor or workload identity, organization role and financial authority, purpose, payer or program profile, object scope, operation scope, and recipient or output scope when applicable.

#### Scenario: Authorized claim operation
- **WHEN** an identified actor requests a claim operation with current authority whose purpose, organization, payer profile, object, operation, and output scopes all match
- **THEN** the system authorizes the operation before execution and records the matched decision inputs

#### Scenario: Payer disclosure recipient is outside scope
- **WHEN** an actor requests a payer disclosure to a recipient not included in the current authority
- **THEN** the system refuses the disclosure before any protected data is released

### Requirement: Authorization fails closed
The system SHALL refuse a protected reimbursement operation when required authority or policy input is missing, expired, revoked, unresolved, or mismatched. A refusal SHALL prevent the protected side effect and SHALL NOT be converted into authority by a public-anchor result or availability state.

#### Scenario: Revoked authority is presented
- **WHEN** an actor presents authority that was revoked before a remittance operation
- **THEN** the system refuses the operation without posting, exposing, exporting, or otherwise applying the requested remittance side effect

#### Scenario: Required policy input is unresolved
- **WHEN** a billing operation requires a payer or program policy input that cannot be resolved
- **THEN** the system refuses the operation rather than applying a default authority assumption

### Requirement: Allowed and refused attempts produce owner-local use events
The system SHALL create an attributable, integrity-verifiable, owner-local use event for every allowed or refused protected reimbursement attempt. The event SHALL bind local object references, actor or workload identity, requested operation, purpose, relevant authority and policy versions, authorization decision reference, decision outcome, decision reason, and event time without requiring sensitive metadata to leave the owner environment.

#### Scenario: Refused attempt is auditable
- **WHEN** a claim operation is refused for an authority scope mismatch
- **THEN** the system records an owner-local use event identifying the attempted operation, applied versions, refusal outcome, and refusal reason

#### Scenario: Allowed attempt is auditable
- **WHEN** a payer-disclosure operation is authorized and executed
- **THEN** the system records an owner-local use event that can be correlated with the disclosure result through local references

### Requirement: Local decision, use event, and mutation are atomic
For an entirely owner-local protected operation, the system SHALL commit the authorization decision, owner-local use event, and authorized local mutation in one atomic transaction. If any part fails, the system SHALL roll back every part and SHALL NOT report success. For a refused operation, the system SHALL durably commit the refusal decision and refusal use event before returning the refusal; failure to persist them SHALL be returned as an operational failure rather than a completed policy decision.

#### Scenario: Use event persistence fails during a local mutation
- **WHEN** an authorized owner-local billing mutation succeeds in memory but its use event cannot be persisted in the same transaction
- **THEN** the system rolls back the mutation and decision and returns failure without reporting the billing operation as successful

#### Scenario: Local mutation fails after authorization
- **WHEN** the authorization decision and use event are prepared but the owner-local claim mutation fails before transaction commit
- **THEN** the system commits none of the decision, use event, or mutation and does not report success

#### Scenario: Refusal event cannot be persisted
- **WHEN** a protected operation is denied by policy but the refusal decision and event cannot be durably committed
- **THEN** the system returns an operational failure and does not represent the refusal audit record as complete

### Requirement: External effects use an idempotent outbox
For a payer, clearinghouse, payment, or other external effect, the system SHALL atomically persist the authorization decision, owner-local intent/use event, pending local mutation, and outbox item before dispatch. The intent/use event SHALL conform to the same attributable use-event requirement as every allowed operation by binding local object references, actor or workload identity, requested operation, purpose, relevant authority and policy versions, the same authorization decision reference, decision outcome, decision reason, and event time. It SHALL also link locally to the external intent and outbox item. The outbox item SHALL carry an owner-local idempotency key for the external intent, and every retry of that intent SHALL reuse the same key.

#### Scenario: External claim submission is queued
- **WHEN** a claim submission is authorized and requires a clearinghouse effect
- **THEN** the system atomically records the decision, a conforming attributable intent/use event with the same decision binding and required use-event fields, pending claim state, and idempotent outbox item before any dispatch attempt

#### Scenario: Outbox dispatch is retried
- **WHEN** a payer, clearinghouse, or payment dispatch is retried after timeout or worker interruption
- **THEN** the system reuses the original intent's idempotency key and does not create a new independently executable effect

### Requirement: External reconciliation creates separate linked outcome events
The system SHALL treat an authoritative external acknowledgement or provider query as reconciliation evidence and SHALL append a separate, attributable, integrity-verifiable owner-local outcome event for each reconciliation result without modifying the original intent/use event. Each outcome event SHALL link to the original intent/use event, authorization decision, external intent, and idempotency key and SHALL bind provider attribution, reconciliation evidence reference, result reason, event time, and exactly one result state: `success`, `failure`, or `indeterminate`. The system SHALL atomically persist the outcome event with its corresponding local state transition.

#### Scenario: Reconciliation confirms external success
- **WHEN** authoritative payer, clearinghouse, or payment reconciliation confirms that the external effect succeeded
- **THEN** the system atomically records a separate linked `success` outcome event and successful local state before reporting success

#### Scenario: Reconciliation confirms external failure
- **WHEN** authoritative reconciliation confirms that the external effect failed or did not occur
- **THEN** the system atomically records a separate linked `failure` outcome event and terminal failure state before reporting the terminal failure

#### Scenario: Reconciliation remains inconclusive
- **WHEN** reconciliation cannot determine whether the external effect succeeded
- **THEN** the system atomically records a separate linked `indeterminate` outcome event, keeps reconciliation required, and does not report success or terminal failure

#### Scenario: Later reconciliation resolves an indeterminate outcome
- **WHEN** a later authoritative reconciliation resolves an external intent that previously produced an `indeterminate` outcome event
- **THEN** the system appends a new linked `success` or `failure` outcome event and preserves the prior intent/use and indeterminate outcome events unchanged

### Requirement: Uncertain external effects become indeterminate and require reconciliation
When an external effect may have succeeded but authoritative reconciliation or its linked owner-local outcome event is absent, the system SHALL record or derive an explicit indeterminate state, SHALL NOT report success, and SHALL require provider reconciliation before asserting a terminal result or issuing any non-idempotent follow-up.

#### Scenario: Payment may succeed before acknowledgement persistence fails
- **WHEN** a payment provider may have applied an authorized effect but the acknowledgement or owner-local outcome event cannot be durably persisted
- **THEN** the system exposes or derives the operation as indeterminate, does not report success, and schedules reconciliation using the original external intent and idempotency key

#### Scenario: Reconciled outcome event persistence fails
- **WHEN** reconciliation determines a success or failure result but the separate linked outcome event and corresponding local state cannot be atomically persisted
- **THEN** the system leaves or derives the operation as indeterminate, reports no terminal result, and repeats reconciliation without modifying the original intent/use event

### Requirement: Access state is independent from custody state
The system SHALL apply access revocation to future protected-operation decisions and SHALL NOT interpret revocation as authorization to delete records, destroy keys, release holds, or clear retention obligations.

#### Scenario: Access is revoked while retention remains active
- **WHEN** an actor's access is revoked for a claim record that has an active retention obligation
- **THEN** the system refuses the actor's future use while leaving the record's custody, obligations, holds, and disposition eligibility unchanged

### Requirement: Authorization contracts remain portable
The system SHALL expose the protected-operation authorization behavior without requiring a Medx-specific product, a particular EHR, a public anchor, or a specific identity, wallet, payer-policy, storage, or key-management provider.

#### Scenario: Non-Medx deployment authorizes an operation
- **WHEN** a deployment supplies conforming local identity, organization-authority, and payer-policy inputs without Medx services
- **THEN** the system evaluates the protected reimbursement operation using the same authorization and event semantics
