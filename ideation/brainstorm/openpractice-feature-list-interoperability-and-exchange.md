# Interoperability And Exchange — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should provide one governed, observable integration spine for EHR-adjacent facts, payer transactions, migrations, and bounded automation so every money-and-data flow follows a versioned contract rather than a database coupling or opaque clearinghouse handoff.
Topics: openpractice-feature-list, interoperability, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Contracted EHR boundary** — openChart supplies versioned FHIR R4 resources and documentation references needed for charge derivation, while signed clinical facts remain authoritative in openChart and direct database reads are prohibited.
- **Payer channels operated as product** — eligibility 270/271, claim 837 transport, remittance 835, and 277/999 status and acknowledgment paths share enrollment, credential, validation, retry, and exception workflows.
- **Coexistence and migration kits** — OpenEMR mapping templates extract and reconcile demographics, coverage, and financial ledgers through declared exports and APIs, with dry runs, provenance, rejects, and human-approved cutover reports.
- **Bounded automation subscriptions** — MedxFactory receives revocable webhook/event scopes tied to explicit capabilities, rate limits, expiry, replay protection, and audit identity, never blanket access or default financial authority.
- **Versioned public surface** — API keys and OAuth clients have owners, scopes, rotation, revocation, usage limits, and deprecation notices while additive versions coexist through published migration windows.
- **Interface operations console** — channel health, latency, schema failures, acknowledgments, dead letters, replay eligibility, and partner incidents are visible without exposing unnecessary PHI in logs.
- **End-to-end transaction trace** — one correlation identity links an eligibility request, appointment context, charge inputs, claim submission, acknowledgments, remittance, and posted payment across every adapter.

## Focus

Which integration capabilities let openPractice own reliable money and administrative data movement while keeping the EHR boundary contractual, clinical truth external, automation bounded, and every failure operable by practice staff?

## Current state: OpenEMR baseline

OpenEMR offers REST and FHIR R4 APIs with OAuth/OIDC and granular scopes, HL7 and C-CDA exchange paths, X12 5010 billing support, clearinghouse-oriented claims and remittance modules, configurable code sets, and import/export utilities. This is a broad toolkit, but practices or implementers still assemble partner enrollment, interface mappings, transport, monitoring, acknowledgment interpretation, retries, migration reconciliation, and upgrade compatibility. Its newer FHIR surface improves standards access, yet the baseline does not by itself create one product-owned operations plane spanning eligibility, claims, remittance, dead letters, API lifecycle, scoped automation, and cross-channel correlation.

Sources: OpenEMR FHIR R4/API authentication documentation and Release Features wiki; athenahealth Developer Portal and authorization guide; eClinicalWorks Connect and practice-management integrations; AdvancedMD developer-solutions and cloud-platform pages; NextGen API, Mirth, and FQHC materials; Tebra Why Tebra platform page; HL7 US Core and CARIN Blue Button implementation guides; X12 transaction-set reference; ONC Health IT Certification Program overview.

## Enterprise gap candidates

- athenahealth exposes a large developer portal, OAuth scopes, FHIR R4/US Core APIs, marketplace distribution, and interface services over a multi-tenant cloud; the product lesson is governed onboarding and workflow reach, not dependence on a proprietary network.
- eClinicalWorks separates provider/backend FHIR access from healow patient and scheduling APIs, documents rate limits, supports practice-management interfaces and user-action-log export, and serves multi-location organizations through centrally administered cloud services.
- AdvancedMD combines a cloud-centered, multi-location database with proprietary Connect APIs and FHIR R4 access; its developer onboarding and production-credential process demonstrate that lifecycle governance matters as much as endpoint count.
- NextGen Enterprise publishes broad administrative read/write APIs, self-service app authorization, FHIR R4 surfaces, and Mirth deployment choices from self-hosted to fully managed; its community-health-center footprint shows the governance and scale expected across complex organizations.
- Tebra/Kareo presents a connected cloud practice platform and third-party EHR integrations for independent practices; openPractice must match low-friction exchange without making cloud-only hosting or a single vendor's connector catalog the architecture.

## Proposed feature set for openPractice

Parity floor: authenticated versioned REST and FHIR R4 APIs, HL7-compatible adapter points, X12-capable clearinghouse transport, configurable code sets, import/export tooling, and role-scoped integration identities. Adopted gaps: contract-tested openChart consumption of versioned Patient, Coverage, scheduling, Encounter, and documentation-reference resources required for administrative and charge workflows, with no direct database access; OpenEMR coexistence and migration templates for demographics, coverage, and ledger extraction; first-party channel operations for 270/271, 837, 835, 277, and 999 transactions; CARIN Blue Button consumption for member-authorized adjudicated claims data where useful, explicitly not as a claim-processing substitute; scoped webhook subscriptions for MedxFactory; API key and client rotation, revocation, ownership, quotas, and rate limits; additive version coexistence with deprecation windows; channel monitoring, dead letters, replay, acknowledgment interpretation, and PHI-minimized diagnostics; and governed annual CPT, HCPCS, and ICD code-set loads with licensing, provenance, effective dates, rollback, and human approval. Twist: every accepted cross-system operation receives a correlation ID propagated end to end, making the path from eligibility inquiry through acknowledgments to posted payment queryable as one trace.

## Interfaces and boundaries

Consumes: versioned FHIR R4 and US Core-aligned resources from openChart or another EHR, member-authorized payer data shaped by CARIN Blue Button where applicable, licensed terminology releases, X12 messages through configured trading partners, and human-approved migration mappings. Emits: normalized administrative facts, payer acknowledgments and work items, bounded events, contract-health metrics, provenance, and correlation traces to scheduling, coding, claims, remittance, and reporting. Owns transport orchestration, contracts, credentials, observability, and replay policy; never owns signed clinical facts, reads another product's database, silently recodes clinical content, or lets an automation post irreversible financial actions without explicit authority and human gates.

## Alternatives and tensions

Direct payer connectivity offers control but multiplies enrollment and companion-guide work; a clearinghouse-first adapter lowers breadth cost but must not hide 277/999 outcomes. FHIR R4 and US Core provide a clinical-adjacent exchange floor, whereas CARIN Blue Button carries adjudicated payer data and does not replace X12 processing. X12 implementation guides and code-set licenses constrain what can be redistributed in open source. Long deprecation windows aid clinic stability but expand test matrices. Replay is essential for recovery yet unsafe for non-idempotent payment posting, so operation-specific idempotency and human confirmation are mandatory. ONC certification criteria apply to particular health IT modules and capabilities; openPractice should maintain conformance evidence where relevant without claiming certification before testing and listing.

## Open questions

- Which openChart FHIR profiles and event schemas form the smallest charge-derivation contract without pulling clinical authorship into openPractice?
- Which payer transactions launch through one clearinghouse adapter, and which need direct-payer paths for community-health-center or enterprise buyers?
- How are correlation IDs bridged when a partner strips custom identifiers or returns asynchronous acknowledgments under a different control number?
- What code-set licensing and update service model preserves open-source redistribution boundaries while guaranteeing effective-date correctness?

## Relationships

Clustered in [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md). Adjacent: [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md), [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md).
