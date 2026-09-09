# Coverage And Authorizations — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should turn coverage certainty into a continuously refreshed pre-service control, joining detailed benefits and authorization state to every scheduled service so staff resolve risk before care becomes a denial.
Topics: openpractice-feature-list, coverage, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Eligibility at every operational checkpoint** — automated X12 270/271 checks at booking, check-in, on demand, and in configurable previsit batches, with freshness and failure states visible on the schedule.
- **Benefit-detail workspace** — normalize payer responses into active coverage, network indicators, copay, coinsurance, deductible, and out-of-pocket accumulators while preserving the source response.
- **Coverage-change watch** — recheck upcoming appointments when prior evidence becomes stale and route changed, inactive, or ambiguous coverage to staff before arrival.
- **Coordination-of-benefits control** — record payer order, subscriber relationships, effective periods, and unresolved COB conflicts without silently choosing financial responsibility.
- **Authorization lifecycle** — assemble requests, submit through supported channels, track status and correspondence, and alert on expirations, approved units, and exhausted visits.
- **Authorization-to-charge gate** — compare planned and posted services with authorization scope so mismatches surface before rendering or claim creation, with explicit staff override and rationale.

## Focus

Which coverage and authorization capabilities make financial clearance a dependable pre-service event rather than a post-denial investigation, without letting automation make clinical or irreversible financial decisions?

## Current state: OpenEMR baseline

OpenEMR stores insurance and eligibility-verification records, can produce batch 270 inquiries, and relies on configured X12 partners or clearinghouse modules for transmission and useful 271 interpretation. ClaimRev can trigger a real-time check when an appointment is created, show payer-specific detail such as deductibles, age results into staleness, and sweep upcoming appointments; older Office Ally/clearinghouse workflows remain configuration-dependent. Prior authorization is offered through a paid module rather than a cohesive core workflow, and baseline OpenEMR does not natively connect authorization dates, units, and procedure scope to scheduling and charge readiness or continuously monitor coverage changes.

Sources: OpenEMR Insurance Eligibility Check wiki; OpenEMR ClaimRev Clearinghouse Module wiki; OpenEMR ClaimRev module catalog page; OpenEMR eligibility_verification data dictionary.

## Enterprise gap candidates

- athenahealth: athenaCollector checks eligibility before appointments and flags ineligible patients; athenaOne authorization tooling combines payer-requirement determination, request work, specialist-service options, status tracking, approved dates/visits, and payer-rule maintenance within operational workflows.
- eClinicalWorks: clearinghouse integrations span eligibility through payment, while its RCM tooling advertises automated and batch eligibility, real-time edits, alerts, appeals, and RPA-assisted repetitive work; healow extends the surrounding patient-facing workflow.
- AdvancedMD: eEligibility runs scheduled batch and on-demand checks, places detailed results into scheduling and insurance surfaces, warns about uncovered charges, and connects front-office findings to a deep financial-reporting environment; encounter-based commercial options reinforce its small-practice fit.
- NextGen Healthcare: specialty-oriented practice management unifies scheduling, automated eligibility, cost estimation, billing, and analytics, surfacing coverage and preauthorization needs while broader population-health tooling supports value-based organizations.
- Tebra/Kareo: the independent-practice product emphasizes real-time 270/271 eligibility, copay/deductible detail, intake/scheduling/billing integration, and straightforward denial prevention; its strength is accessible automation rather than enterprise authorization complexity.

## Proposed feature set for openPractice

Parity floor: insurance policy history, payer and plan configuration, on-demand and batch 270/271 exchange through pluggable clearinghouses, preserved raw responses, normalized eligibility status and benefit detail, manual verification evidence, COB ordering, and authorization records with attachments and audit history. Adopted gaps: booking/check-in/previsit eligibility policies; deductible, out-of-pocket, copay, coinsurance, network, and service-level benefit views; stale-result and coverage-change monitoring; COB conflict worklists; authorization requirement discovery, request assembly, channel-aware submission, status/correspondence tracking, expiration and unit-consumption alerts; and authorization-to-scheduled-service-to-charge matching with staff-confirmed exceptions. Twist: every upcoming appointment carries an explainable coverage confidence score — evidence freshness, response completeness, COB certainty, and authorization fit — that drives prioritized staff worklists without autonomously cancelling care or assigning liability.

## Interfaces and boundaries

Consumes: registrations and policy data from registration-and-demographics, appointments and planned administrative services from scheduling, payer transactions through interoperability connectors, and only the minimum supporting clinical artifacts explicitly released by openChart or another EHR for an authorization request. Emits: clearance status and staff tasks to scheduling, authorization identifiers and constraints to charge-capture-and-coding, and auditable eligibility evidence to estimates and claims. Owns operational verification and authorization state; never owns orders, encounters, diagnoses, medical-necessity decisions, or signed clinical content, and MedxFactory actions remain capability-scoped with human gates for exceptions and submissions that create external commitments.

## Alternatives and tensions

Direct payer connections can improve timeliness but multiply certification and maintenance compared with a clearinghouse-first strategy. A 271 is payer-specific evidence, not a payment guarantee, so normalized benefit displays must retain caveats and raw provenance. Frequent rechecks improve certainty but add transaction cost and noisy worklists. Hard stops reduce preventable denials but can obstruct care; default to visible escalation, role-authorized override, and reason capture rather than autonomous cancellation. Authorization assembly needs clinical evidence, yet openPractice must request references from the source EHR instead of copying or editing the chart.

## Open questions

- Which service-type codes and freshness windows should default by appointment type, payer, and specialty?
- Should v1 support X12 278 and payer APIs directly, portal-assisted work only, or a clearinghouse abstraction across all channels?
- How should the confidence score expose uncertainty without implying guaranteed coverage or payment?
- What policy governs authorization-unit reservation when several scheduled services could consume the same approval?

## Relationships

Clustered in [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md). Adjacent: [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md), [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md).
