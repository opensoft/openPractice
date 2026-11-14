# Claims And Remittance — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should operate the payer pipeline as one observable, provenance-linked product—from preflight edits through acknowledgments, remittance, denials, and timely filing—while certified clearinghouse connectors remain replaceable transport rails.
Topics: openpractice-feature-list, claims, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Pre-submission claim control** — versioned payer and practice edit rules scrub 837P/837I batches, explain each hold, and preserve overrides with actor, reason, and timestamp.
- **Acknowledgments as work** — TA1, 999, and 277CA responses become in-app claim events and actionable queues rather than files hidden in clearinghouse portals.
- **Continuous claim status** — scheduled and on-demand 276/277 retrieval identifies stalled, rejected, accepted, adjudicated, and payer-unlocatable claims.
- **Remittance exception operations** — 835 ingestion proposes service-line payments, adjustments, and patient responsibility while mismatches, reversals, and ambiguous remits wait for review.
- **Denial resolution workbench** — normalized CARC/RARC reasons drive ownership, appeal deadlines, evidence-reference assembly, corrected-claim or appeal paths, and monitored resubmission loops.
- **Payer accountability** — scorecards expose rejection, denial, underpayment, response-latency, overturn, and touch-rate patterns by payer, plan, location, and service family.

## Focus

Which capabilities let openPractice run claims and remittance as a first-party operational system instead of making billers reconstruct one lifecycle across the practice platform, clearinghouse portals, payer sites, and spreadsheets?

## Current state: OpenEMR baseline

OpenEMR provides X12 5010 electronic billing, professional and institutional claim support including UB-04, batch X12 generation, claim-management and AR interfaces, manual EOB entry, 835/ERA import and posting, secondary billing, adjustments, patient invoices, and payment workflows. Transmission and return traffic depend on configured clearinghouses: the ClaimRev module can send claims, download 999/277 responses, expose a lightweight claim-status search, and deliver ERA access, while other integrations may require portal upload or SFTP. The core therefore covers substantial claim production and posting but does not present a clearinghouse-neutral acknowledgment lifecycle, mature CARC/RARC denial operations, systematic appeal evidence assembly, payer scorecards, or a native timely-filing control plane.

Sources: OpenEMR Features wiki; Accounting & Receivables 4.1 wiki; OpenEMR 6.x Billing — Claims Workflows wiki; ClaimRev Clearinghouse Module wiki; ClaimRev billing-claim-submissions module page.

## Enterprise gap candidates

- athenahealth combines a frequently updated payer-rules network with pre-submission alarms, claim-status visibility, follow-up workflows, remittance posting, denial guidance, payer benchmarking, and service teams that work unpaid or denied claims.
- eClinicalWorks combines claim-edit automation, scrubbing and submission bots, electronic-remittance posting, denial and appeal tooling, timely-filing prioritization, central-billing work queues, and financial analytics; its clearinghouse integrations also surface claim status and reports.
- AdvancedMD pairs configurable claim edits and an integrated clearinghouse with adjudication tracking, automated ERA matching/posting, denial and unexpected-payment worklists, AR control, and broad configurable reporting; some predictive denial-and-appeal capabilities arrive through a partner integration.
- NextGen Healthcare and Tebra package claims automation, denial visibility, remit posting, analytics, and enterprise or multi-practice billing workflows around their practice platforms; Epic Resolute PB is the enterprise reference for tightly joined professional billing, claim/remit operations, and patient financial workflows.

## Proposed feature set for openPractice

Parity floor: deterministic 837P/837I batch generation, X12 validation, clearinghouse submission adapters, TA1/999/277CA ingestion, 276/277 status retrieval, 835 import with reviewable service-line posting, secondary/corrected claims, adjustments, and complete claim-event history. Adopted gaps: a versioned claim-edit rules engine with payer-specific explanations; acknowledgment and status queues inside the product; configurable status polling and stale-claim monitors; ERA auto-posting thresholds with exception queues and deposit reconciliation; CARC/RARC-normalized denial workbench with ownership, deadlines, appeal templates, chart-evidence references, resubmission loops, and payer-performance scorecards; timely-filing clocks that account for claim history and payer policy. Twist: a provenance API can answer every hop from denial to claim, charge, source event, edit decision, acknowledgment, remit, and appeal without transferring ownership of the clinical facts it references.

## Interfaces and boundaries

Consumes: approved charges and source-event identifiers from charge-capture-and-coding; coverage, payer, contract, and filing-policy data from registration and revenue-cycle configuration; FHIR R4 financial and document references from openChart or another EHR; X12 traffic from certified clearinghouse connectors. Emits: adjudication and patient-responsibility outcomes to patient-billing-and-payments, denial and payer signals to reporting, and bounded work capabilities to MedxFactory. Owns claim lifecycle state, financial posting proposals, deadlines, and audit provenance; it does not own encounters, signed notes, orders, results, medication data, payer adjudication, or clearinghouse network certification.

## Alternatives and tensions

Direct payer connectivity maximizes control but creates certification and operating burden; one hub partner simplifies launch but risks lock-in, so the claim state machine should remain connector-neutral. Aggressive auto-posting improves throughput but can conceal reversals or malformed remits, requiring confidence thresholds, balancing controls, and reversible batches. A curated denial taxonomy aids operations but must retain original CARC/RARC codes. Appeal assistance may assemble authorized chart references, never infer or alter clinical facts, and submission authority must remain explicit.

## Open questions

- Which clearinghouse capabilities must be available at launch across 837, TA1/999/277CA, 276/277, attachments, and 835, and which may arrive through staged connector certification?
- Should edit rules be centrally curated, tenant-authored, learned from outcomes under review, or layered in that precedence order?
- What balancing and approval thresholds permit safe ERA auto-posting while preserving reversal and replay semantics?

## Relationships

Clustered in [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md). Adjacent: [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md), [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md), [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md).
