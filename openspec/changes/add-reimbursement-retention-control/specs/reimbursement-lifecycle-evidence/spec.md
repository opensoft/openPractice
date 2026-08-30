## ADDED Requirements

### Requirement: Reimbursement lifecycle events remain owner-local
The system SHALL retain descriptive authorization, use, obligation, successor, hold, disposition, storage, and key-management events in the owner's environment. Each event SHALL carry local correlation references, relevant policy or authority versions, event type, outcome or state transition, actor or provider attribution, and event time sufficient to reconstruct the governed lifecycle.

#### Scenario: Claim lifecycle is reconstructed locally
- **WHEN** an authorized reviewer follows a claim from use authorization through a successor obligation, hold release, disposition decision, and provider execution result
- **THEN** the system correlates the owner-local events without relying on public metadata

### Requirement: Public envelopes conform to a selected version-pinned neutral profile
When public witnessing is enabled, the system SHALL select and pin an exact version of a neutral public-envelope profile and SHALL serialize public-anchor payloads that conform exactly to that version. The system SHALL NOT submit a payload when the profile or version is unselected, unresolved, or unsupported. A public envelope MUST contain only the opaque commitment and fields permitted by the pinned profile and MUST NOT include a stable owner-local correlation identifier or patient, payer, claim, program, provider-type, protected-operation, purpose, recipient, record-family, legal-policy, hold, exact retention horizon, or disposition metadata.

#### Scenario: Lifecycle event is prepared for public witnessing
- **WHEN** an owner-local lifecycle event or checkpoint is selected for a public witness under a supported, version-pinned neutral public-envelope profile
- **THEN** the system derives an opaque commitment and serializes exactly the fields allowed by that profile version without descriptive metadata or stable owner-local correlation identifiers

#### Scenario: Anchor adapter requests descriptive labels
- **WHEN** a public-anchor adapter requests a claim identifier, payer label, program label, or retention date in addition to an opaque commitment
- **THEN** the system refuses to include the descriptive fields in the public payload

#### Scenario: Public-envelope profile version is not pinned
- **WHEN** public witnessing is requested without one supported neutral public-envelope profile version selected and pinned
- **THEN** the system does not create or submit a public payload and leaves the owner-local lifecycle decision unchanged

### Requirement: Local public-envelope storage is not a superset
The system SHALL store or regenerate the serialized public envelope according to the exact pinned profile schema and SHALL NOT define a local public-envelope superset containing private association fields or stable correlatable identifiers. The local outbox MAY retain private associations among the owner-local event, envelope, dispatch attempts, and receipts, but those associations SHALL be stored separately and SHALL NOT be serialized into the public envelope.

#### Scenario: Outbox associates a local event with an envelope
- **WHEN** the owner-local outbox links a lifecycle event to a profile-conformant public envelope
- **THEN** the system keeps the event reference and retry or receipt associations in private outbox metadata separate from the exact serialized public envelope

#### Scenario: Local serializer receives an extra correlation field
- **WHEN** a local component attempts to add an owner-local event identifier, outbox identifier, tenant identifier, or other stable correlation field to the public-envelope object
- **THEN** the system rejects the non-conformant envelope rather than retaining or submitting a public superset

### Requirement: Public witnessing is asynchronous
The system SHALL enqueue public witnessing only after the owner-local event is durably recorded and SHALL process profile-conformant submission, retry, and private receipt association asynchronously. Public-anchor delay, failure, or absence SHALL NOT authorize an operation, release custody, clear an obligation or hold, approve disposition, or invalidate an otherwise completed owner-local decision.

#### Scenario: Public anchor is unavailable after an allowed operation
- **WHEN** an allowed claim operation has a durable owner-local use event and the public anchor is unavailable
- **THEN** the system retains a retryable witness request without reversing the operation or changing its authorization decision

#### Scenario: Public receipt arrives later
- **WHEN** a public witness receipt arrives after the related owner-local lifecycle event was completed
- **THEN** the system associates the receipt through separate private outbox metadata without adding local correlation or descriptive fields to the public envelope

### Requirement: Public proof semantics are limited
The system SHALL describe a public witness as evidence that committed bytes existed no later than the witnessed point and can be checked for consistency with the opaque commitment. The system MUST NOT describe the witness as proof that reimbursement was proper, authority was legally sufficient, records were physically retained, a hold was validly released, or data and keys were universally destroyed.

#### Scenario: Reviewer inspects a destruction commitment
- **WHEN** a reviewer verifies a public commitment associated locally with a provider destruction event
- **THEN** the system presents the commitment as integrity and existence evidence for the scoped event, not as proof of universal destruction

### Requirement: Lifecycle evidence contracts remain anchor-neutral
The system SHALL support owner-local lifecycle evidence when no public-anchor adapter is configured and SHALL NOT require a specific chain, witness cadence, or external evidence provider for authorization, retention, hold, or disposition behavior.

#### Scenario: Deployment has no public anchor
- **WHEN** a deployment does not configure a public-anchor adapter
- **THEN** the system continues to record and evaluate owner-local lifecycle events with the same authorization, retention, hold, and disposition semantics
