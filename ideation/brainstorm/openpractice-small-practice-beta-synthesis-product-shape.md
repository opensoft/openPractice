# Synthesis: Small-Practice Product Shape — Brainstorm

Status: brainstorm
Kind: architecture
Summary: the launch archetype, normalized capability catalog, and thin-slice sequencing combine to make openPractice a small-practice operating product with an explicit beta finish line rather than a flat collection of enterprise and experimental feature ideas.
Topics: openpractice-small-practice-beta, product-shape, launch-archetype, feature-normalization, beta-scope, synthesis
Repository context: openPractice — product and release framing for the first small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Archetype-driven scope test** — retain a candidate only when it changes an observable outcome for the selected small-practice actors or provides a required foundation.
- **Capability-layer roadmap** — sequence authoritative domains and platform controls before channels and optional intelligence that depend on them.
- **One beta finish line** — accept the release through a complete administrative and financial narrative rather than unrelated feature completion percentages.
- **Evidence-backed deferral** — preserve valuable companion-app, AI, prediction, sensing, enterprise, and segment ideas with stated prerequisites instead of deleting or prematurely implementing them.

## Members and their joints

Atomic members: [Small Ambulatory Practice Beta Archetype](openpractice-small-practice-beta-launch-archetype.md), [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md), and [Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md).

```text
small-practice actors and constraints
                 │
                 ▼
 normalized domains, services, controls and channels
                 │
                 ▼
 foundational slice → access slice → revenue slice → exception readiness
                 │
                 ▼
       explicit beta outcome and deferral boundary
```

### Archetype ↔ catalog

The archetype supplies the test for catalog relevance. Role-compressed staff make capability-based authorization and concise exception work more important, not less. Limited implementation capacity elevates guided configuration, migration reconciliation, manual fallbacks, and explainable status. A connected EHR makes clinical-boundary contracts foundational. A small deployment does not justify removing audit, financial control, or recovery safeguards.

The normalized catalog prevents channel choices from redefining ownership. Registration owns administrative identity regardless of whether staff, web, kiosk, or a companion app collects a change. Claims owns claim lifecycle while interoperability owns X12 transport. Patient billing owns an obligation and payment authority while communications owns the decision and attempt to notify the patient.

### Catalog ↔ sequencing

Capability classes expose prerequisites. A native app cannot safely link subjects until identity, representation, consent, and task contracts exist. AI suggestions cannot be evaluated until the deterministic form and submission workflow is stable. Sensor arrival cannot improve flow until arrival states, manual correction, and metrics work. Denial prediction cannot be meaningful before claims, remittance, denial disposition, and metric semantics produce reliable history.

Sequencing therefore follows authority and evidence, not novelty. Shared practice structure, identity, security, documents, integrations, work, and financial primitives come first; a complete access and professional revenue loop follows; advanced exception automation and optional experiences build on observed pain.

### Archetype ↔ beta exit

The beta finish line is operational independence for one bounded practice and connector set. Staff should complete ordinary and exceptional work without database edits, private product-team procedures, or patient dependence on one device. The revenue-cycle thesis requires tracing one real transaction path through reconciliation, while the portability thesis requires normal operation, backup, restore, and evidence on a standard deployment.

## Emergent behavior

The three atomics transform a broad strategic vision into a decision system. New ideas can be placed at the correct layer, tested against the launch customer, and either included with an observable beta outcome or deferred with prerequisites. The result supports disciplined exploration without pretending that brainstorm status is a delivery commitment.

## Tensions to hold

A complete thin slice crosses more domains than an access-only prototype and therefore exposes integration risk early. Strictly optimizing for one design partner can improve product fit while accidentally encoding local quirks as universal behavior. Small-practice simplicity must come from defaults and progressive disclosure, not a flat data model that prevents later growth. Manual fallbacks enable beta operation but can hide labor unless the product measures their frequency, age, and reason.

## Recombination opportunities

[Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md) supplies the organizational, provider, referral, evidence, access, and implementation foundations implied by the product shape. [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md) completes the existing eligibility-to-payment thesis with contract expectation, attachments, reconciliation, and accounting boundaries.

## Open questions

- Which design-partner facts should be treated as reusable archetype constraints versus tenant-specific configuration?
- What evidence is sufficient to move a deferred channel or intelligence feature into beta scope?
- Does the first beta milestone need the entire revenue loop in production, or may certified partner test traffic prove part of it before a later live milestone?
- Which small-practice outcome should dominate sequencing when access, cash flow, staff burden, and implementation time conflict?
