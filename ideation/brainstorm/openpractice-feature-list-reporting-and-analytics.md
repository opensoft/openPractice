# Reporting And Analytics — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should make operational and financial performance reproducible through open, versioned metric semantics, role-scoped drill-downs, and portable exports rather than leaving every practice to reinterpret opaque dashboard numbers.
Topics: openpractice-feature-list, reporting, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Practice KPI pack** — fill rate, no-show rate, days in AR, first-pass claim yield, denial rate, net collection rate, and time-of-service collection rate ship with definitions, freshness, and accountable owners.
- **Explainable drill-downs** — every aggregate can move from practice to location, provider, payer, queue, and permitted transaction detail without changing the governing formula.
- **Open metric registry** — formulas, inclusions, exclusions, source fields, effective versions, tests, and revision notes are published so a number can be cited and reproduced outside the product.
- **Role-scoped team views** — saved filters, dashboards, and subscriptions can be shared with named teams while financial and patient-level detail remains least-privilege.
- **Scheduled export center** — governed recurring CSV and machine-readable deliveries record parameters, metric versions, recipients, de-identification mode, and completion status.
- **Forward-looking operations views** — payer mix, schedule capacity, collections, and revenue forecasts expose assumptions and confidence ranges instead of presenting predictions as settled facts.

## Focus

Which reporting foundation lets a clinic move from daily operational detail to board-level performance and fair peer comparison while ensuring that every financial or access metric means the same thing across users, exports, and software releases?

## Current state: OpenEMR baseline

OpenEMR includes appointment and encounter reports, cash receipts, collections and accounts-receivable data, insurance distribution, billing history, user-sensitive financial reporting permissions, database views, and form/SQL-oriented custom reporting. This is a useful reporting floor, but advanced analysis generally requires report customization, SQL knowledge, spreadsheets, or external BI; there is no governed semantic metric registry, formula versioning, role-scoped dashboard-sharing model, consistent practice-to-location-to-provider-to-payer drill path, or benchmark contract. Audit logs and access controls exist, yet self-managed deployments still carry responsibility for hardening, export governance, retention, and review.

Sources: OpenEMR Reporting wiki; OpenEMR Accounting wiki; OpenEMR Access Controls Listing; OpenEMR Database Structure; OpenEMR Users Guide database views and reports sections.

## Enterprise gap candidates

- athenaOne offers customizable performance dashboards, report building and scheduling, cross-practice reporting under permission, SQL-oriented Data View access, and network-informed benchmarks; it shows the leverage of broad comparative data but leaves room for publicly reproducible metric contracts.
- eClinicalWorks combines financial and administrative dashboards, scheduled secure report delivery, payer-mix and AR views, multi-site performance views, RPA-fed operations signals, and healow Insights payer/provider data exchange; its population and quality breadth is EHR-side context, while openPractice should retain only operational and financial analytics.
- AdvancedMD advertises more than 150 standard reports plus configurable reporting, drag-and-drop dashboards, scheduled delivery, exports, and filters by provider, carrier, location, and time; breadth and self-service are a clear parity target without adopting closed formulas.
- NextGen provides enterprise operational and financial analytics with prebuilt exploration, drill-downs by location/provider and other parameters, transaction-level detail, peer comparison, and automated reporting; its value-based views illustrate demand for contract-aware performance while clinical quality stays outside openPractice.
- Tebra/Kareo packages role-sensitive workflow dashboards, KPI and AR views, provider/location/payer drill-downs, exports, and customizable practice-revenue analytics for independent practices; approachable defaults matter as much as enterprise depth.

## Proposed feature set for openPractice

Parity floor: OpenEMR-equivalent appointment, encounter-linkage, cash-receipt, collections, AR, payer-distribution, billing-history, audit, and permission-scoped reporting with dependable CSV/PDF-style outputs. Adopted gaps: a canonical KPI dashboard pack for fill rate, no-show rate, days in AR, first-pass claim yield, denial rate, net collection rate, and time-of-service collection rate; consistent drill-down from practice to location, provider, payer, queue, and authorized records; scheduled export center with delivery audit; role-scoped saved views and team sharing; operational cohort exports for non-clinical outreach; payer-mix and revenue forecasting views with visible assumptions; benchmark-ready comparison packages carrying denominator, period, timezone, attribution, and metric-version metadata. Twist: metric semantics are an open, versioned product surface—each formula and conformance test is publishable, exportable, and citable so the same governed inputs produce the same result in every supported release and board deck.

## Interfaces and boundaries

Consumes: appointments, eligibility outcomes, authorization states, queue events, claims, remittances, patient-balance activity, payments, payer contracts where authorized, and organizational dimensions from openPractice; signed administrative status feeds from connected systems through governed interfaces. Emits: dashboards, alerts, benchmark packages, scheduled exports, operational outreach cohorts, and aggregate APIs subject to role and purpose. Owns operational/financial metric semantics and analytic projections; does not calculate eCQMs, clinical quality, risk scores, diagnoses, care gaps, or other EHR-governed clinical measures.

## Alternatives and tensions

A warehouse improves historical stability and heavy-query isolation but adds latency and duplicated protected data; live semantic queries improve freshness but can burden transactional workflows and allow backfilled records to shift history. Public formulas increase trust yet expose differences from vendor or payer definitions, requiring explicit aliases rather than silent equivalence. Benchmarking is useful only when attribution, specialty, practice scale, period, and denominator are comparable. Shared saved views aid teamwork but can become an indirect disclosure channel without row-level authorization and export controls.

## Open questions

- Which KPI versions form the first compatibility promise, and what changes require a new major metric version?
- Should historical dashboards recompute under the latest formula or default to the formula effective when the period closed?
- Can benchmark aggregates be useful at launch without a broad network, or should comparison begin with customer-defined peer groups and imported targets?

## Relationships

Clustered in [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md). Adjacent: [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md), [Revenue Cycle cluster docs](openpractice-feature-list-synthesis-revenue-cycle.md), [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md).
