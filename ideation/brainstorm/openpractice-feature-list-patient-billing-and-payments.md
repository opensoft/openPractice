# Patient Billing And Payments — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should turn patient receivables into a transparent, consent-driven path from estimate through statement, authorized payment, and reconciliation, replacing encounter-by-encounter dunning with understandable choices and audit-grade financial control.
Topics: openpractice-feature-list, patient-billing, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **One understandable balance** — unified cross-encounter statements separate charges, payer activity, adjustments, prior payments, and current responsibility while retaining line-level drilldown.
- **Estimate before obligation** — contracted rates, scheduled services, coverage, and benefit responses produce versioned pre-service estimates with assumptions, ranges, expiry, and variance explanations.
- **Patient-directed payment choices** — card, ACH, and supported digital-wallet acceptance joins partial payment, payment-plan, and financial-assistance handoffs without coercive defaults.
- **Consent-bound saved methods and autopay** — tokenized payment methods and scheduled charges operate only within recorded scope, amount or cap, timing, notice, revocation, and authorization history.
- **Controlled corrections** — refunds, voids, credits, adjustments, and balance transfers require reason codes, role-appropriate approval, immutable audit events, and ledger reconciliation.
- **Gentle receivables orchestration** — preference-aware statement and reminder cadences coordinate with communications, pause on dispute or hardship, and surface point-of-service prompts without blocking care by default.

## Focus

Which patient-billing capabilities make responsibility understandable and collection convenient while preserving patient consent, staff authority, accounting discipline, and a complete explanation of every dollar?

## Current state: OpenEMR baseline

OpenEMR can post insurance and patient payments, distribute batch receipts to encounter invoices, enter adjustments, generate customizable patient invoices or statements after insurance processing, vary later notices, record billing notes, track receivables and collections, and connect payment gateways. Its workflow remains largely encounter/invoice centered: a practice can print or portal-notify statements and record payment, but native unified cross-encounter presentation, contracted-rate plus benefit-response estimates, structured payment plans, consent-governed saved methods and autopay, coordinated digital dunning, and estimate-to-payment reconciliation are absent or dependent on local customization and external services.

Sources: OpenEMR Features wiki; Accounting & Receivables 4.1 wiki; OpenEMR 6.x Billing — Claims Workflows wiki.

## Enterprise gap candidates

- athenahealth supports expected-copay and balance collection at check-in, guest and portal payment flows, full or partial payments, payment-plan views, saved-card agreements, receipts, digital reminders, and card-on-file authorization controls.
- eClinicalWorks offers secure text or email statements, online payment links without portal login, check-in and kiosk collection, and automatic payment posting into practice management, coordinating reminders with the billing cycle.
- AdvancedMD combines integrated in-person and online card processing with ACH and digital-wallet options, statements, reminders, saved methods, payment plans, recurring schedules, transaction reporting, receipts, and automated posting; its reporting breadth supports operational reconciliation.
- NextGen Healthcare joins pre-service cost estimation, guest and portal pay, saved card or bank methods, automatic payment plans, real-time posting, batching, and payment analytics; Tebra/Kareo emphasizes small-practice integrated statements, card-on-file authorization, card/ACH/mobile-wallet acceptance, recurring payments, reminders, and reconciliation without double entry.
- Epic's patient financial experience is the enterprise reference for combined professional and hospital estimates and balances, pre-service arrangements, capped authorization for later charging, personalized payment plans, digital wallets, and preference-aware statement delivery.

## Proposed feature set for openPractice

Parity floor: patient ledgers, cross-encounter guarantor balances, itemized unified statements, paper and electronic delivery, cash/check/card posting, receipts, adjustments, credits, refunds, aging, collections status, and reconciliation to deposits and the general ledger. Adopted gaps: versioned estimates calculated from contracted rates, scheduled charge expectations, and 271 benefit responses; estimate acceptance or acknowledgment without presenting estimates as guarantees; card, ACH, and digital-wallet adapters using tokenized payment methods; explicit saved-method consent; patient-selected payment plans; scheduled autopay constrained by recorded authorization, advance notice, caps, and revocation; role-gated refunds and adjustments; point-of-service prompts at check-in; gentle-dunning cadences coordinated with communications and paused by disputes, hardship, or active plans. Twist: estimate-to-payment lineage makes every collected dollar traceable to the estimate version, statement obligation, authorization, payment event, allocation, receipt, and later refund or adjustment it fulfilled.

## Interfaces and boundaries

Consumes: adjudicated patient responsibility from claims-and-remittance, contracted rates and coverage data from revenue-cycle configuration, 270/271 benefits through interoperability, scheduled service identifiers from scheduling, communication preferences and consent records, and payment-processor tokens rather than raw account data. Emits: estimates and collection prompts to scheduling/check-in, approved messages to patient-engagement-and-communications, settled allocations and exceptions to accounting, and bounded capabilities to MedxFactory. Owns receivables, statements, authorizations, payment allocation, refunds, and financial audit lineage; it does not own clinical services, encounters, signed facts, benefit adjudication, or unrestricted payment credentials.

## Alternatives and tensions

A unified statement improves comprehension but must preserve legal entity, provider-group, guarantor, and merchant boundaries. Estimates create trust only when assumptions and later variances are visible; false precision is worse than a candid range. Saved methods reduce friction but cannot become blanket permission: scheduled autopay runs only under recorded patient authorization, and no agent may initiate new money movement outside that scope. Refund automation could shorten resolution time, but refunds and material adjustments require human approval and reconciliation safeguards. Collection prompts must support clinic policy without turning financial status into an implicit clinical-access decision.

## Open questions

- Which contracted-rate, eligibility, and benefit inputs are sufficient for a useful estimate range at launch, and how should stale or missing data be disclosed?
- Should payment plans be practice-funded only, or may optional external financing appear behind separate consent and disclosure boundaries?
- Which refund, adjustment, and autopay thresholds require dual approval, and how are failed or disputed transactions paused and escalated?

## Relationships

Clustered in [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md). Adjacent: [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md), [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md), [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md).
