# Synthesis: Reimbursement Evidence Lifecycle — Brainstorm

Status: brainstorm
Kind: architecture
Summary: Combining operation-level authority with independent retention and hold state can preserve reimbursement evidence without granting indefinite access or allowing premature destruction.
Topics: reimbursement-retention-control, reimbursement-evidence-lifecycle, billing-use, legal-retention, synthesis
Repository context: openPractice administrative authority, retention, and public evidence joints
Captured: 2026-08-29

## Possible feats

- **Claim-to-disposition evidence chain** — trace authority, use, correction,
  appeal, retention, hold, and final disposition without putting claim details
  on a public chain.

## Members and their joints

Atomic members:
[Billing and Reimbursement Use Control](reimbursement-retention-control-billing-use.md),
[Retention, Legal Hold, and Disposition](reimbursement-retention-control-legal-retention.md).

```text
authorized billing operation -> signed use event
             |                       |
             v                       v
       workflow state       retention obligations
             |                       |
             +------ hold/reopen ----+
                                     |
                              disposition review
                                     |
                           storage/key event evidence
```

### Access versus custody

Wallet revocation and role changes govern future use. Retention obligations
govern custody. A user can lose access while the practice remains legally
required to preserve the record; the two outcomes must never collapse into one
state.

### Correction and reopening

Claim corrections, appeals, overpayment reviews, payer audits, and litigation
create successors and extensions. Accepted history remains addressable, and a
new obligation set records why the disposition horizon changed.

### Public evidence

Owner-local events may be committed through the shared Kaspa-first and daily
Bitcoin witness profile. Public proofs show that signed lifecycle evidence
existed and remained unchanged, not that reimbursement was proper or data was
physically retained or erased.

## Emergent behavior

The combined model lets openPractice minimize active access while preserving
records for reimbursement, audit, and legal duties. It also makes a final
disposition explainable against every obligation that had to clear first.

## Tensions to hold

- Fraud and audit analytics benefit from long history; privacy and least-
  privilege goals favor minimization.
- Legal rules change and can be disputed, so policy provenance and human
  approval matter as much as automated calculation.
- A cryptographic erasure event may satisfy one disposal strategy but conflict
  with required restoration or litigation preservation.

## Recombination opportunities

The pattern can support Medicaid claims, Medicare records, commercial payer
contracts, tax documents, patient billing, and vendor delegation through
different approved policy profiles.

## Open questions

- What common lifecycle vocabulary is portable across payer programs?
- Which events need immediate external witnessing versus inclusion only in the
  daily checkpoint?
