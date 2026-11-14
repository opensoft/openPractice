# Small Ambulatory Practice Beta Archetype — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should treat a small group practice and a small clinic as one first-beta archetype: a compact ambulatory organization whose staff share roles, lacks deep implementation capacity, and needs one coherent administrative and financial operating loop around a connected EHR.
Topics: openpractice-small-practice-beta, launch-archetype, practice-strategy, feature-roadmap
Repository context: openPractice — first-beta product framing for a small group practice or small clinic using a connected EHR
Captured: 2026-08-26

## Possible feats

- **One small-practice template** — configure a usable organization, location, provider roster, roles, service offerings, schedules, payer connections, and financial defaults without an enterprise implementation project.
- **Role-compressed workspaces** — let one authorized person move safely among front-desk, scheduling, billing, and practice-administration responsibilities without receiving blanket access.
- **Exception-led daily work** — show the small team what is blocked, late, unmatched, or financially risky instead of requiring staff to inspect every record.
- **Complete operating loop** — prove registration through payment and reconciliation for one practice before expanding into enterprise breadth.
- **Assisted-channel parity** — keep staff, telephone, web, kiosk, and paper-assisted paths available so the beta does not assume every patient installs an app.

## Focus

What customer archetype should constrain the first beta so feature decisions optimize for a real practice rather than an abstract union of independent clinics, multi-site enterprises, FQHCs, billing services, and hospital systems?

## Captured direction

The stated first-beta customer is a small group practice or small clinic. For product purposes these are one archetype, called a **small ambulatory practice** in this packet. The label describes operating shape rather than a final provider-count threshold.

The archetype has a compact staff in which people commonly wear several administrative roles, limited or outsourced IT capacity, little appetite for custom implementation, and a connected EHR that remains authoritative for clinical facts. It may have more than one provider and location, but it does not need enterprise hierarchy, delegated credentialing, centralized billing across many organizations, or hospital-inpatient revenue-cycle breadth in the first beta.

US-first operation is a strong working inference from the repository's HIPAA, CPT, X12, US Core, and US payer emphasis, but it is not treated here as a ratified geography decision. Exact size, specialty, payer mix, EHR, clearinghouse, and payment-processor assumptions remain open.

## Operating model

The minimum actor set is deliberately small:

| Actor | Typical beta responsibilities | Authority boundary |
|---|---|---|
| Patient or authorized representative | Supply administrative facts, choose appointments, acknowledge forms and estimates, initiate authorized payments | May act only for the verified subject and effective authority granted |
| Front-desk or scheduler | Register, correct administrative records, book, check in, collect evidence, and resolve ordinary access exceptions | Cannot adjudicate identity merges, clinical appropriateness, or unrestricted financial changes |
| Biller or revenue owner | Review charges, claims, remittances, denials, statements, and payment exceptions | Cannot author clinical facts or approve authority beyond assigned financial limits |
| Practice administrator | Configure the practice, assign bounded roles, approve policies and exceptional actions, and monitor operations | Does not gain silent access to all PHI merely by being an administrator |
| Clinician or EHR operator | Author clinical facts and release bounded references needed for administrative and financial work | Clinical authorship remains in openChart or another connected EHR |
| Interface or support operator | Monitor connectors, retry eligible failures, and assist implementation | Cannot inspect unnecessary PHI or replay non-idempotent money movement |

The product should assume that some of these responsibilities belong to the same human while still enforcing separate capabilities, reauthentication, approval thresholds, and audit purpose.

## Interfaces and boundaries

This archetype constrains product scope and usability; it does not define a tenant schema, pricing plan, legal practice classification, or universal staffing model. It consumes the repository's clinical boundary, portability, security, and human-authority principles and informs every beta feature's defaults, setup burden, and manual fallback.

The beta should not require a native patient app, AI model, lobby hardware, dedicated interface engineer, proprietary cloud service, billing bureau, or full ERP. Those capabilities may integrate or follow later. HealthLinc remains a working patient-channel concept rather than a beta prerequisite, and the name requires clearance because an existing healthcare organization identifies HealthLinc as its registered name and trademark in its [published brand standards](https://healthlincchc.org/wp-content/uploads/BrandBook_2025_8_27-WEB.pdf).

## Alternatives and tensions

A precise provider and location ceiling makes packaging and performance expectations clearer, but a premature number may exclude the actual design partner. Supporting multiple locations from the data model prevents early lock-in, while enterprise-grade organization hierarchies would bury a small practice in configuration. A single staff member may legitimately hold several roles, yet collapsing those roles into one broad administrator permission would defeat minimum-necessary access. Choosing a specialty early can sharpen workflows, but specialty-specific clinical logic must remain outside openPractice.

## Open questions

- What working envelope should beta testing use for providers, locations, concurrent staff, appointments, and monthly claims?
- Is the design partner US-based, and should US-first be made an explicit product decision rather than an inference?
- Which specialty or mixed-specialty profile best exercises the administrative workflow without pulling clinical logic into openPractice?
- Which EHR, clearinghouse, payment processor, and communication vendor form the first bounded connector set?
- Must the companion app participate in the beta, or are responsive web, kiosk, and assisted workflows sufficient for the first complete loop?

## Relationships

Combined with [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md) and [Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) in [Synthesis: Small-Practice Product Shape](openpractice-small-practice-beta-synthesis-product-shape.md). The archetype also constrains [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md).
