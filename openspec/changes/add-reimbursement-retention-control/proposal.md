Status: draft

## Why

openPractice needs a portable control boundary for sensitive reimbursement operations and the records they create, because authorization can end while custody duties continue through retention, audit, appeal, reopening, or legal hold. Defining that boundary before the first runtime implementation prevents jurisdiction-specific assumptions, public metadata leakage, and unsupported claims about destruction from becoming foundational behavior.

## What Changes

- Add owner-local authorization decisions and use events for billing, claim, remittance, and payer-disclosure operations, without requiring Medx-specific products or a particular EHR.
- Make each owner-local authorization decision, attributable use event, and local mutation one atomic outcome; for payer, clearinghouse, and payment side effects, atomically create a conforming intent/use event and idempotent outbox item, then append a separate linked outcome event after reconciliation, including explicit success, failure, or indeterminate state without reporting uncertain success.
- Add versioned, sourced retention obligations with successor obligations for reopening, appeal, audit, investigation, or other approved extensions.
- Separate access revocation from custody retention, and make legal or audit holds block deletion and key destruction without granting access.
- Add a disposition gate that permits a scoped storage or key action only after every applicable obligation and hold has cleared and an authorized reviewer approves it.
- Record provider destruction as a scoped claim about the authorized operation, never as universal proof that every plaintext, replica, backup, derivative, or key copy is gone.
- Block automated duration calculation whenever jurisdiction is unselected, and keep exact Medicaid duration profiles disabled until a named jurisdiction, program, provider type, effective date, authoritative source, and human approval are present; this change defines no legal duration.
- Allow owner-local lifecycle events to receive asynchronous public witnesses only through a selected, version-pinned neutral public-envelope profile containing opaque commitments and no stable owner-local correlation identifiers.
- Require a durable, unconditional owner ratification after strict OpenSpec validation and before creating exactly one Speckit feature.
- Require repository constitution adoption as separate governance work and a meaningful Constitution Check before the Speckit plan phase; do not implement governance as part of the reimbursement feature.
- Keep all runtime implementation inside openPractice and its single Speckit worktree; external products expose bounded contracts only and require separate governed work for any runtime change.
- Archive this OpenSpec change only after the single feature's implementation is merged and its validation evidence is accepted.

## Capabilities

### New Capabilities
- `reimbursement-operation-authorization`: Portable, atomic authorization decisions and owner-local use events for billing, claim, remittance, and payer-disclosure operations, including reliable external-effect handoff and reconciliation.
- `retention-obligation-control`: Versioned retention obligations, holds, successor obligations, and disposition gating independent of access state.
- `reimbursement-lifecycle-evidence`: Scoped lifecycle evidence and version-pinned neutral public envelopes for asynchronous opaque commitments without public reimbursement metadata, correlatable local identifiers, or universal destruction claims.

### Modified Capabilities

None.

## Impact

- Establishes future openPractice contracts and persisted records for operation authorization, policy provenance, obligation evaluation, hold state, disposition decisions, and lifecycle events.
- Introduces integration seams for human-approved jurisdiction/program policy profiles, idempotent external-effect delivery and reconciliation, storage and key-management executors, and optional asynchronous public-anchor adapters.
- Requires future tests to cover refused and allowed operations, atomic local rollback, uncertain external outcomes, policy activation prerequisites, unselected jurisdiction, successor obligations, hold precedence, disposition eligibility, event scoping, public-envelope conformance, and public payload minimization.
- Adds governance gates and acceptance evidence inside this OpenSpec change while leaving constitution adoption and all executable implementation planning to their separate governance and single-Speckit-feature homes.
- Does not add runtime code, dependencies, legal rules, payer profiles, or public-chain implementations in this change proposal.
