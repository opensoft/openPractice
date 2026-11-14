# Financial Control And Accounting Bridge — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice should own a balanced healthcare subledger and reversible reconciliation workflow from remittance or patient payment through deposit, settlement, allocation, credit, refund, period close, and general-ledger export while leaving general accounting and treasury custody to connected systems.
Topics: openpractice-small-practice-beta, financial-control, patient-billing, claims, cost-accountability
Repository context: openPractice — financial integrity and accounting boundary for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Balanced healthcare subledger** — represent charges, contractual adjustments, payer and patient responsibility, receipts, allocations, transfers, credits, refunds, and reversals as immutable balanced entries.
- **Deposit and settlement workbench** — group cash, checks, card batches, ACH, digital-wallet settlements, payer EFTs, and fees into expected and observed settlement events.
- **EFT-to-ERA reassociation** — connect deposits and remittance batches by trace evidence while routing ambiguous, missing, duplicate, or reversed cases for review.
- **Unapplied and credit-balance control** — make unidentified receipts, overpayments, patient credits, payer credits, and refund liabilities visible with aging and ownership.
- **Controlled close and correction** — freeze a period's financial interpretation, require reasoned correcting entries, and preserve every prior state.
- **Balanced accounting export** — publish versioned journal mappings and export batches to ERP or general-ledger systems with acknowledgment, rejection, and reconciliation status.

## Focus

Which financial controls are necessary for the practice-management ledger to explain every dollar without expanding openPractice into a complete accounting, banking, tax, or payroll product?

## Proposed model

The domain distinguishes operational financial events from accounting destinations:

```text
claim adjudication or patient obligation
                 │
                 ▼
        receipt / remit / adjustment
                 │
       ┌─────────┴──────────┐
       ▼                    ▼
allocation to obligation   deposit or processor settlement
       │                    │
       └─────────┬──────────┘
                 ▼
          reconciled batch
                 │
                 ▼
      balanced general-ledger export
```

An immutable journal records economic events; workflow states control proposals, approvals, posting, reconciliation, reversal, and export. Corrections append reversing and replacement entries rather than mutating posted history. Domain records carry legal entity, location, merchant, payer or patient source, service and posting dates, currency, correlation identity, source evidence, approver, and export destination.

The beta should support at least:

- payer payments and adjustments proposed from 835 remittance;
- patient cash, check, card, ACH, or supported wallet receipts without storing raw payment credentials;
- payment allocation across guarantor obligations under explicit rules;
- deposit and processor settlement totals, fees, chargebacks, and exceptions;
- unapplied cash and credit-balance aging;
- role-gated refund, void, write-off, transfer, and material adjustment proposals;
- period-aware reports and balanced journal export with a receiving-system acknowledgment.

OpenPractice remains the source for healthcare receivable lineage. The connected accounting system remains the source for the general ledger, bank accounts, taxes, payroll, enterprise budgeting, and final financial statements. Reconciliation proves the boundary rather than duplicating it.

## Interfaces and boundaries

Consumes approved charges, claim adjudication, patient obligations, payment-processor tokens and settlement events, bank or deposit evidence where configured, role and approval policy, legal-entity context, and accounting mappings. Emits allocations, balances, refund liabilities, reconciliation work, close evidence, deposits, receipts, and balanced journal batches to patient billing, claims, reporting, operations, and accounting adapters.

It never stores raw card or bank credentials, initiates new money movement without recorded authority, silently nets unrelated legal entities or merchants, deletes posted entries, or becomes a general ERP. Scheduled payments run only inside patient-approved scope; refunds and material adjustments retain human approval and reversal controls.

## Alternatives and tensions

Using an ERP ledger directly reduces duplication but couples healthcare workflows to accounting internals and weakens traceability from claim line to payment allocation. A healthcare subledger adds responsibility but preserves domain semantics and permits replaceable accounting systems. Automatic remittance posting and settlement matching improve throughput while increasing replay, reversal, and false-match risk. Locking periods improves reporting stability but small practices need understandable correction paths when late remittances arrive.

## Open questions

- Which accounting system or neutral journal format should receive the first beta export?
- What minimum double-entry model supports payer and patient transactions without importing a full chart of accounts?
- Which ERA, EFT, processor, and deposit matching thresholds may post automatically, and which require review?
- What approval thresholds apply to refunds, write-offs, transfers, chargebacks, and material adjustments when only a few staff members exist?
- Does beta require bank-feed reconciliation, or are processor, clearinghouse, and staff-confirmed deposit records sufficient?

## Relationships

Combined with [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md) and [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md) in [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md). It sharpens the accounting boundary in the existing [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md) and [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md) documents.
