# Billing and Reimbursement Use Control — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice could authorize and audit each protected claim, billing, remittance, payer-disclosure, and reimbursement operation through purpose-bound wallet authority and owner-local policy.
Topics: reimbursement-retention-control, billing-use, openpractice, reimbursement, claims, audit
Repository context: openPractice portable medical-practice financial and administrative enforcement
Captured: 2026-08-29

## Possible feats

- **Reimbursement-use gate** — require current authority for claim creation,
  submission, correction, appeal, remittance access, and payer disclosure.
- **Administrative attempt audit** — retain signed evidence of allowed and
  refused operations without exposing claim metadata publicly.

## Focus

This document isolates use control for openPractice's administrative and
financial records. These records may contain PHI, payer identifiers, account
data, and legally significant reimbursement history even though they are not
the clinical chart.

## Proposed model

```text
billing or claim operation
  -> actor/workload identity
  -> wallet grant and exercise
  -> organization role and financial authority
  -> purpose and payer/program policy
  -> local openPractice decision
  -> allow or refuse
  -> signed administrative-use event
  -> asynchronous public evidence checkpoint
```

The operation vocabulary could include register, schedule, verify coverage,
request authorization, capture charge, assemble claim, submit, correct, appeal,
post remittance, bill patient, collect payment, disclose, export, audit, and
dispose. Each use event binds the owner-local object references and policy
versions while public anchoring receives only an opaque commitment.

Medicaid, Medicare, commercial-payer, and self-pay contexts should be profile
inputs rather than public labels. Even naming a payer program on a public chain
could expose sensitive relationships.

## Interfaces and boundaries

openPractice owns financial and administrative operation semantics, local role
and authority checks, workflow state, allow/refuse decisions, and use events.
It does not own signed clinical facts, patient consent law, neutral wallet
contracts, public chain adapters, or jurisdiction-specific legal conclusions.

MedxFactory or another domain policy package may supply purpose mappings and
cross-product authority. MedxPractice may bind deployment and program profiles.
The public openPractice product must remain usable with another EHR and without
MedxFactory.

## Alternatives and tensions

- One broad billing grant simplifies operations but gives excessive access
  across claims, remittances, and patient balances.
- Fine-grained grants reduce blast radius but can obstruct high-volume revenue-
  cycle workflows unless role and batch semantics are explicit.
- Failed-attempt audit supports fraud detection but creates sensitive employee,
  patient, and payer behavior data that needs its own retention controls.

## Open questions

- Which operations require patient authority, organization authority, payer
  authority, or dual authority?
- How are clearinghouses, billing vendors, auditors, and AI coding services
  represented as recipients?
- Which bulk operations need stronger scope and output controls?

## Relationships

Retention and legal-hold state are explored in
[Retention, Legal Hold, and Disposition](reimbursement-retention-control-legal-retention.md).
Their combined lifecycle is related in
[Synthesis: Reimbursement Evidence Lifecycle](reimbursement-retention-control-synthesis-evidence-lifecycle.md).
