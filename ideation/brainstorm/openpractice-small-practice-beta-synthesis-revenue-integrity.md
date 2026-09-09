# Synthesis: Revenue Integrity And Financial Close — Brainstorm

Status: brainstorm
Kind: architecture
Summary: administrative evidence, payer contract expectations, and a balanced healthcare subledger complete openPractice's revenue-cycle provenance by connecting what should have been paid, what evidence was sent, what actually settled, and what reached accounting.
Topics: openpractice-small-practice-beta, revenue-integrity, administrative-documents, payer-contracts, financial-control, synthesis
Repository context: openPractice — evidence, reimbursement expectation, settlement, and accounting relationships for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Expected-to-settled trace** — navigate contract term → expected allowable → claim and attachment → remit line → deposit → allocation → accounting export.
- **Evidence-backed variance work** — distinguish payer underpayment, valid adjustment, missing attachment, contract-data defect, claim defect, and unresolved uncertainty before pursuing recovery.
- **Closed financial loop** — reconcile payer and patient receipts to settlements and balanced exports instead of treating remittance posting as the end of revenue cycle.
- **Correction without erasure** — preserve contract, attachment, posting, reversal, refund, and journal versions so every later correction retains its original path.

## Members and their joints

Atomic members: [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md), and [Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md).

```text
effective contract and participation
                │
                ▼
       expected reimbursement
                │
charge → claim ──┼── attachment evidence and acknowledgments
                │
                ▼
          adjudication / remit
             │          │
             │          └── variance and recovery work
             ▼
       receipt and settlement
                │
                ▼
 allocation → reconciliation → balanced accounting export
```

### Contract expectation ↔ claim and remit

The expected result must resolve the agreement, participation, fee, modifier, location, provider, and service-date versions that applied to the claim line. Remittance retains original CARC/RARC and payer detail. A variance engine compares the two but does not decide contract meaning by itself; qualified staff classify the difference and correct either the source contract data or the claim workflow when necessary.

### Attachment evidence ↔ adjudication

Requests and transmitted evidence join the claim event history, allowing denial and delay work to determine whether information was requested, authorized, sent, accepted, incomplete, or late. The evidence layer provides integrity, routing, minimum-necessary access, and retention while the EHR remains authoritative for clinical content. Claims-attachment standards can evolve independently from prior-authorization attachments.

### Remit ↔ settlement ↔ accounting

An 835 proposal does not prove that cash settled, and a bank or processor deposit does not explain patient or claim allocation. The financial-control layer joins transaction traces, balances postings, exposes ambiguity, manages reversals and credits, and sends balanced journal batches to the accounting owner. Period close freezes the interpretation used for reports while compensating entries preserve late corrections.

### Human authority ↔ automation

Deterministic contract selection, attachment validation, remit matching, and journal balancing can support straight-through work within configured thresholds. Uncertain contract interpretation, disputed underpayment, ambiguous evidence, material adjustment, refund, unmatched settlement, or non-idempotent replay remains human-owned. Every threshold and override is versioned and reviewable.

## Emergent behavior

The cluster answers both “why did this payer pay this amount?” and “where did the money go?” from one supported provenance path. It turns denial and underpayment recovery into evidence-backed work, prevents unbalanced operational posting from leaking into accounting, and lets a small practice close the period without maintaining parallel spreadsheets as the hidden source of truth.

## Tensions to hold

Contract modeling can become an unbounded legal rules engine. Attachment retention can create a second clinical repository. Automatic posting and matching can hide reversals or duplicates. Period close can frustrate correction of late payer activity. The design must return explicit uncertainty, retain source evidence and versions, use compensating entries, and let policies choose when manual review is worth its operational cost.

## Recombination opportunities

This cluster extends the existing [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md), whose coverage, charge, claim, and patient-receivable pipeline supplies the core transactions. Provider participation comes from [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). Metric definitions and variance work feed the existing [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md), while attachment and journal connectors consume the existing [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md).

## Open questions

- Which contract terms, attachment transactions, settlements, and accounting exports must be live in the first beta milestone?
- What amount or uncertainty makes a reimbursement variance actionable for a small practice?
- Which clinical attachment evidence may be retained after successful transmission, and for how long?
- What period-close behavior preserves reproducible metrics while accommodating late remittance, takebacks, and refunds?
- Which automated posting and matching thresholds are safe with a limited separation-of-duties staff model?
