# Charge Capture And Coding — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should produce complete, defensible charges from administrative events and immutable encounter references, combining versioned fee logic, missing-charge control, and cited coding assistance without taking ownership of clinical documentation.
Topics: openpractice-feature-list, charge-capture, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Versioned fee administration** — effective-dated charge and allowable schedules by entity, location, provider, payer class, and contract, with approvals, comparisons, and rollback-safe history.
- **Governed code catalog** — licensed CPT plus HCPCS, ICD-10-CM, modifiers, revenue codes, local codes, units, and superbill groupings managed with release provenance and activation windows.
- **Event-derived draft charges** — deterministic rules create reviewable charge candidates from administrative events and received procedure, infusion, order, or encounter references rather than requiring duplicate entry.
- **Missing-charge worklists** — reconcile completed scheduled visits and other billable events against posted, held, intentionally omitted, or externally billed charges.
- **Defensible coding assistance** — suggest draft codes with confidence and exact citations into received documentation, never silently post them, and require qualified human confirmation.
- **Modifier and bundling checks** — explain likely missing, conflicting, or payer-sensitive modifiers and code combinations before a charge reaches a claim.

## Focus

Which controls let openPractice capture every legitimate charge once, price it under the correct effective rules, and preserve its evidence trail while clinical authorship remains entirely in openChart or another connected EHR?

## Current state: OpenEMR baseline

OpenEMR provides configurable fee sheets and superbill groups, CPT/HCPCS/ICD code support, modifiers, units, diagnosis justification, revenue codes, multiple price levels, code history, and encounter-centered charge entry feeding professional and institutional claim generation. The workflow is principally manual: staff select services and diagnosis pointers on each encounter's fee sheet, while code imports, local catalogs, prices, and payer contingencies require administration. It lacks a native cross-event missing-charge workbench, effective-dated contract-grade fee governance, documentation-cited coding suggestions, and a durable provenance query spanning source event through claim; automated coding or richer charge capture generally depends on custom rules, external services, or modules.

Sources: OpenEMR Preparing for Billing and Using the Fee Sheet wiki; OpenEMR Billing Setup Howtos; OpenEMR Fee Sheet and Charge Capture documentation; OpenEMR Features wiki.

## Enterprise gap candidates

- athenahealth: athenaCollector monitors practice activity so encounters become charges and claims, applies a large continuously maintained payer-rules network, and now presents documentation-, order-, and practice-rule-informed coding assistance inside the practice-management workflow.
- eClinicalWorks: its Clinical Rules Engine can derive billing codes from captured visit data, RCM tooling analyzes documentation for E&M/CPT recommendations, advanced claim edits test modifier and medical-necessity patterns, and reporting compares fee-schedule allowances with payments; RPA carries repetitive billing work.
- AdvancedMD: online charge slips, charge-code masters, macros, multiple effective-dated fee-schedule versions, carrier/provider exceptions, ClaimInspector edits, and scheduling-linked charge flow create a deep configurable financial workflow; integrated partners add mobile missing-charge alerts and code suggestions.
- NextGen Healthcare: specialty and group-practice tooling includes charge and expected fee schedules, superbill templates, payer-specific effective-dated modifier libraries, pending-charge processing, missing-billing reports, configurable claim edits, and value-based analytics around the broader financial operation.
- Tebra/Kareo: the independent-practice platform keeps charge entry, policy validation, claim scrubbing, submission, and denial correction in one approachable workflow; its comparative advantage is usable integrated billing for small practices rather than highly specialized chargemaster governance.

## Proposed feature set for openPractice

Parity floor: fee sheets and superbills; CPT/HCPCS/ICD-10 and modifier catalogs; units, diagnosis pointers, revenue codes, charge entry and review; multiple price levels; import/update controls; professional and institutional claim readiness; and complete audit history. Adopted gaps: effective-dated charge and allowable schedules with contract/provider/location overrides and approval workflow; deterministic draft-charge rules for administrative events plus received procedure, infusion, order, and encounter references; reconciliation worklists comparing completed appointments and source events with posted charges; superbill templates and batch review; code-, unit-, modifier-, bundling-, age-, sex-, place-of-service-, and authorization-consistency checks; and AI-assisted draft coding that records model/version, confidence, cited source passages, reviewer identity, changes, and mandatory human confirmation under upcoding-monitoring policies. Twist: a supported charge-to-order provenance API answers “everything between this visit and this claim,” returning immutable source references, rule executions, human decisions, fee versions, charge mutations, and downstream claim links.

## Interfaces and boundaries

Consumes: completed administrative events from scheduling, active coverage and authorization constraints from coverage-and-authorizations, governed code and fee data, and immutable encounter, order, procedure, and documentation references received from openChart through FHIR R4-preferred contracts or equivalent EHR adapters. Emits: approved charges and evidence links to claims-and-remittance, missing-charge and coding-review tasks to operational worklists, and provenance/audit events to reporting. Owns financial derivation, pricing, charge state, and coding-review workflow; never creates or edits encounters, diagnoses, orders, clinical facts, or signed documentation. Suggested diagnosis or procedure codes are financial drafts only, with human confirmation required before posting; MedxFactory may prepare or route work only within bounded capability-aware scopes and cannot autonomously approve charges.

## Alternatives and tensions

Rules tied to discrete source events are explainable but require stable cross-EHR contracts; text-based assistance covers more cases but raises provenance, licensing, bias, and upcoding risk. Central fee governance improves consistency yet multi-entity groups need controlled local exceptions. A strict missing-charge comparison can mistake cancelled, global-period, capitated, bundled, research, or intentionally no-charge services for leakage, so every exception needs a reason and lifecycle. Copying clinical text would simplify coding review but create a second chart; retain source identifiers, hashes, and authorized citations instead, resolving full context in the owning EHR.

## Open questions

- Which FHIR resources and extension profile form the minimum viable encounter-to-charge contract with openChart?
- Who may approve fee-schedule releases, coding suggestions, modifier overrides, and intentional no-charge dispositions by practice size?
- Should coding assistance begin with deterministic edits only, or permit a model behind specialty-specific evaluation and audit thresholds?
- How should the provenance API represent corrected, voided, split, bundled, and rebilled charge lineages without losing the original path?

## Relationships

Clustered in [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md). Adjacent: [Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md), [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md).
