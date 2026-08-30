# Retention, Legal Hold, and Disposition — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice could model versioned reimbursement-retention obligations and legal holds separately from access revocation, with destruction permitted only after every applicable obligation is cleared.
Topics: reimbursement-retention-control, legal-retention, openpractice, medicaid, legal-hold, cryptographic-erasure
Repository context: openPractice billing-record lifecycle and jurisdiction/program policy seam
Captured: 2026-08-29

## Possible feats

- **Retention obligation ledger** — bind each record family to versioned legal,
  payer, contract, audit, tax, and operational obligations.
- **Disposition gate** — refuse deletion or key destruction while any hold or
  retention basis remains active.

## Focus

This document isolates retention and disposition for practice records,
including Medicaid reimbursement evidence. It does not assume one universal
duration: obligations can vary by jurisdiction, program, provider type,
contract, audit, claim status, patient age, litigation, and effective date.

## Proposed model

```text
record or record family
  -> applicable obligation set
  -> retention start and calculation basis
  -> claim/appeal/reopening extensions
  -> legal or audit holds
  -> eligibility review
  -> authorized disposition
  -> storage and key events
  -> signed evidence checkpoint
```

Each obligation should identify its authority source, version, jurisdiction,
program class, covered artifact families, trigger, minimum duration or
calculation rule, supersession rule, and review owner. Exact legal rules remain
human-approved policy data rather than constants embedded in application code.

Revoking patient or workforce access does not authorize destruction. A legal
hold ordinarily suspends deletion and key destruction; it is not a trigger for
cryptographic erasure. Claim reopening, appeal, investigation, or audit may
create successor obligations without rewriting prior history.

When every obligation is cleared, a disposition decision may authorize storage
deletion, key or wrapper destruction, or both. A provider-signed destruction
event records the claimed scope. It cannot prove all backups, plaintext,
derived outputs, replicas, or unknown key copies disappeared.

## Interfaces and boundaries

openPractice owns record-family lifecycle, obligation evaluation, hold state,
disposition decisions, and evidence. Legal and compliance authorities approve
policy content. Storage and KMS services perform deletion or key operations.
openxFactory can witness opaque events but cannot define Medicaid or retention
law.

HIPAA documentation retention rules are not a blanket medical-record retention
period. Medicaid and state-specific rules require researched, cited profiles
before implementation. This brainstorm records the required mechanism, not a
legal conclusion.

## Alternatives and tensions

- Key destruction can make remaining ciphertext impractical to recover but may
  conflict with backup, audit, appeal, or legal-hold duties.
- Per-object obligation evaluation is precise; record-family policies are more
  operable but require careful exceptions.
- Publicly anchoring exact dates or program labels can leak sensitive metadata;
  opaque commitments preserve later proof with less disclosure.

## Open questions

- Which Medicaid jurisdictions and provider types define the first profiles?
- Who may impose, renew, challenge, and release a legal hold?
- How are immutable backups expired without claiming instantaneous deletion?
- Which evidence is shown to patients, payers, regulators, and litigants?

## Relationships

The operations subject to these obligations are explored in
[Billing and Reimbursement Use Control](reimbursement-retention-control-billing-use.md).
Their lifecycle is related in
[Synthesis: Reimbursement Evidence Lifecycle](reimbursement-retention-control-synthesis-evidence-lifecycle.md).
