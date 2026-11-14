## ADDED Requirements

### Requirement: Retention policy profiles are versioned and sourced
The system SHALL represent each retention policy profile as an immutable version that identifies its authority source, jurisdiction, program, provider type, effective-date scope, covered record families, retention trigger and calculation rule, supersession semantics, review owner, and human approval state. A later version SHALL NOT silently rewrite an obligation created from an earlier version.

#### Scenario: Approved policy version is superseded
- **WHEN** a new approved policy version supersedes an earlier version
- **THEN** the system preserves the earlier version and keeps existing obligation instances linked to the version and calculation inputs originally applied

### Requirement: Exact Medicaid duration profiles remain disabled until fully qualified and approved
The system MUST keep an exact Medicaid duration profile disabled unless it names the jurisdiction, Medicaid program, provider type, effective date, authoritative source, and human approval. The system MUST NOT infer a duration from a generic Medicaid, HIPAA, payer, or medical-record label and MUST NOT provide an uncited fallback legal rule.

#### Scenario: Medicaid profile lacks a named provider type
- **WHEN** an exact Medicaid duration profile has a jurisdiction, program, effective date, source, and approval but no named provider type
- **THEN** the system keeps the profile disabled and does not use it to calculate a disposition horizon

#### Scenario: Medicaid profile lacks human approval
- **WHEN** an exact Medicaid duration profile is sourced and fully scoped but has not received the required human approval
- **THEN** the system keeps the profile disabled and records that it is ineligible for obligation calculation

#### Scenario: Generic HIPAA label is supplied as a duration rule
- **WHEN** a user attempts to activate an exact record-retention duration based only on a generic HIPAA label
- **THEN** the system refuses activation because the label is not a fully qualified, sourced, and approved retention profile

### Requirement: Unselected jurisdiction blocks duration automation
The system SHALL treat jurisdiction-dependent duration applicability as unresolved when no named jurisdiction is selected. While jurisdiction is unresolved, the system SHALL NOT select a jurisdictional profile, calculate an automated retention duration or disposition horizon, or authorize disposition based on elapsed time.

#### Scenario: Record family has no selected jurisdiction
- **WHEN** a record family reaches a potential retention trigger without a named selected jurisdiction
- **THEN** the system records unresolved duration applicability and does not calculate an automated retention duration or disposition horizon

#### Scenario: Disposition is requested without jurisdiction selection
- **WHEN** disposition is requested for a record family whose jurisdiction-dependent retention applicability remains unselected
- **THEN** the disposition gate refuses authorization even if another generic duration appears to have elapsed

### Requirement: Applicable policies create traceable obligation instances
The system SHALL create owner-local obligation instances that bind a record or record family to the applied policy version, retention trigger, calculation inputs, resulting obligation state, review state, and provenance. Unknown or unresolved applicability SHALL block disposition rather than be treated as no obligation.

#### Scenario: Approved profile applies to a record family
- **WHEN** a fully qualified and approved policy profile applies to a claim record family and its trigger occurs
- **THEN** the system creates an obligation instance linked to the record family, policy version, trigger evidence, and calculation inputs

#### Scenario: Policy applicability is unresolved
- **WHEN** the disposition gate cannot determine whether a retention profile applies to the requested record-family scope
- **THEN** the system marks evaluation unresolved and refuses disposition

### Requirement: Reopening and appeal extensions are successor obligations
The system SHALL represent a reopening, appeal, audit, investigation, litigation, or other approved extension as a successor obligation linked to its predecessor, trigger evidence, authority, and policy version. Creating a successor SHALL preserve predecessor history and SHALL cause evaluation to include the successor.

#### Scenario: Closed claim enters appeal
- **WHEN** an authorized appeal trigger applies to a claim whose original obligation remains recorded
- **THEN** the system creates a linked successor obligation and retains the predecessor without rewriting its history

#### Scenario: Reopening occurs after prior eligibility review
- **WHEN** a claim is reopened after a prior disposition review found the predecessor obligation cleared
- **THEN** the system records the reopening successor and blocks a new disposition authorization until the successor clears

### Requirement: Legal and audit holds are separate from access and obligations
The system SHALL record legal and audit holds as separately authorized, owner-local state with scope, authority, reason reference, impose time, status, and release evidence. A hold SHALL NOT grant access, restore revoked authority, or erase obligation history.

#### Scenario: Legal hold is imposed after access revocation
- **WHEN** an authorized custodian imposes a legal hold on records whose prior user access is revoked
- **THEN** the system preserves the revoked access state and separately records the active hold

#### Scenario: Hold is released
- **WHEN** an authorized custodian releases an active hold with release evidence
- **THEN** the system records the release without clearing or changing any independent retention obligation

### Requirement: Active holds block deletion and key destruction
The system SHALL refuse storage deletion, key destruction, and wrapper destruction for any requested scope covered by an active legal or audit hold. No elapsed retention horizon, access revocation, or prior disposition review SHALL override an active hold.

#### Scenario: Key destruction is requested under active hold
- **WHEN** key or wrapper destruction is requested for ciphertext covered by an active legal hold
- **THEN** the system refuses the request and leaves the key or wrapper operation unauthorized

#### Scenario: Retention duration elapsed under audit hold
- **WHEN** every duration-based obligation has cleared but an applicable audit hold remains active
- **THEN** the system refuses both record deletion and key destruction

### Requirement: Disposition requires a complete gate evaluation and approval
The system SHALL authorize a disposition only when every applicable obligation is cleared, no applicable hold is active, policy applicability is resolved, and an authorized reviewer approves a specific record-family scope and action. The authorized action SHALL distinguish storage deletion, key or wrapper destruction, and a combined action.

#### Scenario: All conditions clear for scoped disposition
- **WHEN** all applicable obligations for the requested record-family scope are cleared, no hold is active, applicability is resolved, and an authorized reviewer approves storage deletion
- **THEN** the system authorizes storage deletion only for the approved scope and action

#### Scenario: One successor obligation remains active
- **WHEN** an original obligation is cleared but one applicable successor obligation remains active
- **THEN** the system refuses the disposition request

### Requirement: Provider destruction evidence is a scoped claim
The system SHALL record a provider destruction event separately from the disposition authorization and SHALL bind it to the approved scope, action, provider, method category, execution result, event time, and related decision. The event SHALL be presented as a provider claim about that execution and SHALL NOT be represented as universal proof that all plaintext, backups, replicas, derivatives, or unknown key copies were destroyed.

#### Scenario: Provider reports successful key destruction
- **WHEN** a key-management provider reports successful destruction for an authorized key scope
- **THEN** the system records the provider's scoped result without asserting destruction of other keys, plaintext, backups, replicas, or derived data

#### Scenario: Provider reports partial execution
- **WHEN** a storage or key-management provider reports a partial or failed disposition action
- **THEN** the system records the scoped result and does not mark unreported or failed scope as destroyed
