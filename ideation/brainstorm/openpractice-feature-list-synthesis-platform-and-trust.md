# Synthesis: Platform And Trust — Brainstorm

Status: brainstorm
Kind: architecture
Summary: security/compliance and interoperability are one trust plane in openPractice — a portable HIPAA control plane and a governed integration spine share identity, audit, correlation, and evidence mechanics so every access decision and cross-system flow is provable on any standard Frappe bench.
Topics: openpractice-feature-list, platform-trust-synthesis, synthesis, platform-security, interoperability
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Trust as shipped control plane** — minimum-necessary RBAC matrix, integrity-chained PHI ledger, reviewed break-glass, and MFA/session policy operate identically across Flutter, Desk, and APIs.
- **One governed integration spine** — FHIR R4 contracts with openChart, X12 payer channels, OpenEMR migration kits, and scoped MedxFactory subscriptions share credentials, monitoring, retry, and replay policy.
- **Correlation as first-class data** — one ID links eligibility inquiry → appointment → charge → claim → acknowledgment → remittance → posted payment, auditable end to end.
- **Evidence generated, not assembled** — signed compliance packs compile from ledger proofs, access reviews, incident worksheets, and restore drills without exporting raw PHI.

## Members and their joints

Atomic members: [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md), [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md).

### Audit ledger ↔ Integration spine

Every cross-system operation emits integrity-chained events carrying the same correlation identity, so interface failures and privacy investigations read from one timeline instead of reconciling adapter logs against audit tables.

### Break-glass ↔ API governance

Emergency human elevation and machine credential lifecycle follow one pattern: time-boxed scope, immediate alert, mandatory review, revocable authority. Partner keys, MedxFactory scopes, and staff break-glass are the same object class with different reviewers.

### Portability ↔ Payer channels

The claim state machine stays connector-neutral so clearinghouse certification never becomes a cloud-only dependency; every control, drill, and evidence path remains operable on-premise per constitution Principle VI.

## Emergent behavior

Compliance stops being an annual documentation project: the system's normal operation continuously produces its own proof. Integration health becomes a visible product surface — stuck transactions, dead letters, partner latency — rather than tickets reconstructed from vendor portals. Trust questions ("who saw this balance, why did this claim stall") get single-query answers.

## Tensions to hold

Rich audit detail creates a second PHI surface requiring redaction discipline. Replay aids recovery but is unsafe for non-idempotent payment posting; operation-specific idempotency and human confirmation are mandatory. External SIEM/WORM adapters strengthen retention but must remain optional above a bench-native floor or portability dies. ONC criteria apply to specific modules — track conformance evidence without advertising blanket certification.

## Recombination opportunities

Evidence packs recombine with [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md) scheduled exports for auditor delivery; correlation traces recombine with [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md) queue provenance; BAA registry recombines with communications vendor failover adapters in [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md).

## Open questions

- Which append-only anchor is the portable default: chained records, signed manifests, or WORM adapters layered?
- How do correlation IDs survive partners that strip custom identifiers or renumber acknowledgments?
- What restore-drill cadence blocks a release versus raising a governance exception?

## Relationships

Owned by [Overview](openpractice-feature-list-overview.md). Cross-cluster: [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md), [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md).
