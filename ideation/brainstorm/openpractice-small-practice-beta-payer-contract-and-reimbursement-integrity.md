# Payer Contract And Reimbursement Integrity — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice should connect effective-dated payer participation and reimbursement terms to expected allowed amounts, remittance variance, underpayment work, and contract evidence so a small practice can detect lost revenue without turning the product into an autonomous contract interpreter.
Topics: openpractice-small-practice-beta, payer-contracts, claims, reporting, cost-accountability
Repository context: openPractice — payer agreement and reimbursement control for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Versioned participation registry** — record which entity, provider, location, product, and service dates a payer agreement covers.
- **Governed reimbursement terms** — represent fee schedules, percentages, case rates, carve-outs, modifiers, exclusions, and precedence with source evidence and approvals.
- **Expected-allowable calculation** — calculate an explainable expectation for a claim line using the contract and charge facts effective on the service date.
- **Remittance variance detection** — compare expected and adjudicated amounts while retaining payer codes, contractual uncertainty, and tolerance policy.
- **Underpayment workbench** — assign research, correspondence, appeal or reconsideration deadlines, evidence, recovery, and closure disposition.
- **Contract performance view** — expose realization, denial, underpayment, turnaround, administrative cost, and renewal observations without making negotiation decisions.

## Focus

How can a small practice know whether payer reimbursement matches its recorded agreements when fee schedules, amendments, provider participation, service dates, and remittance adjustments change over time?

## Proposed model

The domain joins several effective-dated objects:

- **Payer Agreement** — parties, products, covered entities, term, renewal and termination dates, evidence, approval, and status;
- **Participation Scope** — provider, entity, location, taxonomy, offering or code family, and effective period;
- **Reimbursement Schedule** — versioned code, modifier, place-of-service, unit, percentage, case-rate, or formula terms with source provenance;
- **Term Override** — a narrower rule with explicit precedence, justification, and effective period;
- **Expected Reimbursement Result** — claim-line inputs, selected agreement and term versions, rule trace, amount or range, uncertainty, and calculation timestamp;
- **Variance Case** — remit line, expected result, contractual and payer codes, materiality, owner, deadlines, evidence, actions, recovery, and disposition.

The calculation should prefer deterministic terms and return uncertainty when the agreement cannot support a reliable amount. A result is an operational expectation, not a guarantee or legal interpretation. Human reviewers decide whether a variance represents an underpayment, a valid adjustment, a contract-data defect, a coding or claim defect, or a case that is not economical to pursue.

Participation state from provider enrollment must be evaluated separately from reimbursement terms. An agreement can exist while a provider or location is not yet effective, and a payment may follow a legacy or individually negotiated term. Historical adjudication must always resolve the versions effective for the service and claim events rather than today's contract.

## Interfaces and boundaries

Consumes practice and provider structure, payer and product dictionaries, approved contract evidence, governed fee and allowable schedules, charges, claims, remittances, corrections, and human determinations. Emits expected amounts, variance cases, renewal and participation work, contract-aware estimate inputs, and aggregate payer performance to charge capture, claims, patient billing, operations, and reporting.

It does not negotiate contracts, provide legal advice, infer missing contractual obligations through a model, alter a remittance, post a recovery automatically, or make patient liability from an unreviewed variance. It may reference licensed code sets and proprietary contracts under access controls but must not redistribute restricted material in the open-source repository.

## Alternatives and tensions

Storing only fee schedules is simpler but misses participation, amendments, carve-outs, and precedence. Encoding every contract clause creates a brittle legal expert system; a bounded reimbursement grammar plus evidence and explicit unsupported cases is safer. Historical payment patterns can identify anomalies when contract data are incomplete, but patterns must remain signals rather than disguised contractual truth. Low-dollar variance work can cost more than recovery, requiring transparent materiality policies without concealing systematic payer behavior.

## Open questions

- Which reimbursement term primitives cover the beta design partner's largest payer contracts?
- Is contract loading manual and template-assisted first, or does the beta require machine-readable payer schedules?
- What tolerance and materiality policies create work, aggregate observations, or suppress noise?
- How should expected amounts interact with estimates when participation or contract evidence is incomplete?
- Who approves contract versions and closes a variance as valid, unrecoverable, or not worth pursuing in a small team?

## Relationships

Combined with [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md) and [Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md) in [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md). It consumes [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md) and extends the existing [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md), and [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md) research.
