# Synthesis: Revenue Cycle — Brainstorm

Status: brainstorm
Kind: architecture
Summary: coverage, charges, claims, and patient receivables are one provenance-linked pipeline in openPractice — financial clearance happens before service, every dollar traces to its estimate and its source event, and humans confirm every consequential step — replacing both clearinghouse-assembled billing cores and services-led RCM whose economics stay opaque.
Topics: openpractice-feature-list, revenue-cycle-synthesis, synthesis, coverage, charge-capture, claims, patient-billing
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Provenance-linked money chain** — eligibility response → authorization → charge → claim edit → acknowledgment → remittance line → denial → appeal → payment → refund traversable as one supported query.
- **Pre-service financial clearance** — coverage confidence, authorization fit, and patient estimates resolve before arrival, converting denials into front-desk conversations.
- **Human-gated automation** — draft codes, proposed postings, and scheduled autopay all carry evidence, confidence, and recorded authorization; qualified humans confirm, agents prepare.
- **Payer accountability surface** — rejection, denial, latency, overturn, and underpayment patterns scored per payer, plan, and service family.

## Members and their joints

Atomic members: [Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md), [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md), [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md).

### Coverage ↔ Charge Capture

Authorization-to-charge matching closes the loop leaders leave open: planned services compare against approval scope before rendering, and mismatches surface with override reasons instead of becoming denials weeks later.

### Charge Capture ↔ Claims

The same edit-rule engine prices and scrubs: modifier/bundling checks run at charge review and again pre-submission, so a rule fires once with one explanation rather than differing silently between stages.

### Claims ↔ Patient Billing

835 adjudication flows straight into unified statements — patient responsibility splits carry line-level drilldown, and estimate-to-payment lineage shows which collected dollars fulfilled which quoted range.

### Coverage ↔ Patient Billing

Benefit responses feed versioned estimates; stale benefits trigger rechecks before statements assert balances the coverage data no longer supports.

## Emergent behavior

The four make "financial data health" measurable per deployment: first-pass yield, denial rate with root causes, days in AR, time-of-service collection rate — all exported as versioned metrics. Clinics see where money stalls; vendors can be judged on pipeline reliability instead of demo-day dashboards.

## Tensions to hold

Owning clearinghouse operations concentrates liability a reseller diffuse — connector-neutral claim state machines keep rails replaceable. Aggressive auto-posting trades throughput against reversal safety; thresholds, balancing, and reversible batches are load-bearing. AI coding assistance invites upcoding scrutiny; citations, reviewer identity, and monitoring are not optional features but license-to-operate.

## Recombination opportunities

The provenance chain recombines with [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md) for board-grade RCM narratives; denial evidence references recombine through [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md) to pull authorized documentation from openChart; coverage confidence recombines into [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md) booking worklists.

## Open questions

- Does v1 launch with one certified clearinghouse adapter plus direct-payer escape hatches, or broader staged certification?
- Which denial taxonomy layer ships first: X12 CARC/RARC native or curated operational overlay?
- What estimate precision floor must benefits data meet before estimates render to patients?

## Relationships

Owned by [Overview](openpractice-feature-list-overview.md). Cross-cluster: [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md), [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md).
