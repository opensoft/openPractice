# Practice Structure And Service Catalog — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice needs a small-practice-first, effective-dated administrative model for organizations, billing entities, locations, providers, resources, and service offerings so every appointment and financial event carries valid operational context without requiring enterprise configuration.
Topics: openpractice-small-practice-beta, practice-structure, scheduling, charge-capture, multi-location
Repository context: openPractice — foundational administrative master data for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Small-practice setup template** — create one organization, billing identity, primary location, role set, and common service offerings through a short guided flow.
- **Effective-dated organization graph** — preserve when legal, billing, location, provider, resource, and department relationships start, change, and end.
- **Administrative service catalog** — give scheduling, authorization, estimation, and charging one stable offering identity without encoding clinical appropriateness.
- **Operational readiness contract** — explain whether a provider, location, resource, or offering may be scheduled or billed and which evidence or configuration is missing.
- **Safe multi-location growth** — support a few locations, time zones, hours, holidays, and local resources without exposing a large-enterprise hierarchy by default.

## Focus

Which administrative master data must exist before registration, scheduling, coverage, claims, permissions, and reporting can produce coherent records for a small practice?

## Proposed model

The beta needs a minimal graph rather than a flat list of facilities and users:

```text
Practice Organization
├── Legal or Billing Entity
│   ├── billing identifiers and pay-to relationships
│   ├── merchant and accounting destinations
│   └── payer participation references
├── Location
│   ├── hours, holidays, timezone and contact channels
│   ├── optional department or operational team
│   └── reservable rooms, equipment and other resources
├── Provider Affiliation
│   ├── provider administrative identity and taxonomy
│   ├── authorized locations and offerings
│   └── enrollment and credential-readiness references
└── Service Offering
    ├── patient-facing name and scheduling attributes
    ├── administrative prerequisites and duration/resource rules
    └── references to authorization, estimate and charge rules
```

Every relationship should have provenance, effective dates, status, and a reason for material changes. Templates may hide unused complexity for a single-site practice, but the persisted model should not conflate a physical location, legal entity, billing provider, rendering provider, merchant, and scheduling resource.

An administrative service offering is the joint across domains. Scheduling uses it to expose safe slots; coverage uses it to select benefit and authorization policies; patient billing uses it to assemble estimate inputs; charge capture maps completed administrative or EHR-referenced events to governed financial rules. The offering may reference an EHR-owned order or procedure vocabulary but cannot determine what care is clinically appropriate.

Core invariants include:

- every appointment identifies the organization, location, offering, subject, timezone, and required resources;
- every charge and claim line resolves its billing and rendering context for the service date;
- deactivated relationships cannot be silently applied to later dates;
- configuration changes never rewrite historical financial or scheduling context;
- cross-entity balances, statements, deposits, and merchant settlements remain separated unless a governed relationship explicitly permits consolidation;
- readiness failures create explainable warnings or work rather than generic validation errors.

## Interfaces and boundaries

Consumes organizational decisions, public provider identifiers, payer participation references, location configuration, and practice-authored administrative offerings. Emits stable context to scheduling, authorizations, charges, claims, patient billing, access control, reporting, and connector configuration.

This foundation does not own clinical orders, clinical service selection, documentation, HR personnel records, payroll, general-ledger chart-of-accounts design, building maintenance, or inventory management. A user account and a provider identity are related but distinct; a staff member may work across locations without becoming a rendering provider.

## Alternatives and tensions

A single Frappe site per practice simplifies tenant isolation, while a more elaborate multi-organization tenant can support MSO or billing-service use later at considerable authorization cost. Flattening legal and physical entities makes setup easier but creates payment, statement, claim, and reporting errors as soon as the practice adds a location or second billing arrangement. A generic service catalog encourages reuse, yet excessive abstraction can become a hidden clinical terminology system. Small-practice defaults should cover common shapes while allowing advanced objects to remain dormant.

## Open questions

- Is one Frappe site expected to represent exactly one independent practice for the beta?
- Which billing, rendering, supervising, ordering, pay-to, merchant, and service-location roles must be distinct in the first data contract?
- Is an optional department object needed at beta scale, or can teams and queues provide sufficient grouping?
- How are service offerings mapped to appointment types and EHR-owned order or procedure references without creating clinical authority?
- Which historical configuration changes require a correction workflow rather than a new effective-dated version?

## Relationships

Combined with [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md).
