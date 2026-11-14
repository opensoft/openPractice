# Mobile Registration And Previsit Completion — Brainstorm

Status: brainstorm
Kind: report
Summary: HealthLinc should let patients complete an appointment-specific previsit journey across devices while openPractice remains authoritative for administrative and financial artifacts and every health-bearing response is rendered from and returned to openChart through versioned FHIR R4-preferred contracts.
Topics: openpractice-feature-list, mobile-registration, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Appointment-shaped previsit plans** — assemble administrative and openChart-authored clinical tasks by appointment type, patient context, and due-before date while displaying one coherent checklist with visible ownership.
- **Guided registration confirmation** — walk patients through demographics, guarantor and subscriber relationships, coverage, and communication details, showing what changed and requiring confirmation before updates become authoritative.
- **Assisted insurance capture** — acquire clear front/back card images, present OCR-derived payer and member fields only as suggestions, and require patient or staff confirmation before structured coverage updates proceed.
- **Governed forms and signatures** — deliver current HIPAA notice acknowledgment, financial policy, consent-to-treat equivalents, estimates, and other administratively configured forms with version, signer authority, timestamp, and immutable evidence.
- **Resumable financial preparation** — show estimates, capture acknowledgment, and tokenize a payment method through an approved processor without storing card data or autonomously charging, refunding, enrolling, or promising payment terms.
- **Readiness-led arrival** — publish previsit completion percentage and blockers to registration worklists, reminders, kiosks, and desk workflows so patients can resume anywhere and authorized staff can finish exceptions at arrival.

## Focus

Which HealthLinc capabilities can complete all previsit paperwork before arrival, preserve kiosk and front-desk parity, and route each administrative or health-bearing datum directly to its single owning backend?

## Current state: OpenEMR baseline

OpenEMR provides native-portal self-registration with demographic and insurance entry, portal forms, patient-submitted updates, appointment reminders, and configurable kiosk or module-supported intake patterns. It can move basic paperwork ahead of arrival, but the baseline does not offer one appointment-versioned task plan with due dates, partial-save and cross-device resume, coverage-card OCR confirmation, estimate acknowledgment, processor tokenization, quantified readiness, or a governed split in which administrative forms persist in practice management while EHR-authored health questionnaires return as clinical content to a separate system of record.

Sources: OpenEMR Project Wiki, “HOWTO: Patient self-registration in the Native Patient Portal v6.1” and “The OpenEMR 6.0+ Patient Portal”; athenahealth Help, “User Guide — Patient Portal,” and athenahealth, “Engage Patients with Digital Self Check-In”; eClinicalWorks, “Check-In”; AdvancedMD, “Patient Kiosk Software for Private Practices”; NextGen Healthcare, “Patient Intake”; Tebra, “Patient Intake Software for Medical Practices”; Epic, “Explore | MyChart is Epic,” and University of Iowa Health Care, “Welcome (Patient-Entered Forms).”

## Enterprise gap candidates

- athenahealth supports mobile online check-in, registration and insurance review, consent signatures, health-history forms, payments, personalized links, staff-owned devices, and insurance-card image analysis that suggests a package; openPractice can separate suggestion from confirmation and publish readiness without merging operational and clinical authority.
- eClinicalWorks healow CHECK-IN sends appointment-linked secure reminders and guides patients through demographics, insurance, consents, questionnaires, medication and history review, payments, and arrival across smartphone, app, portal, and kiosk; its breadth is the parity benchmark, while its mixed content reinforces the need for explicit backend routing.
- AdvancedMD carries demographics, customizable intake and consent forms, signatures, and patient payments through an iPad kiosk integrated with its portal and EHR, demonstrating the value of in-office parity when mobile work is incomplete.
- NextGen Patient Engage sends appointment-type-specific forms, collects demographics, insurance, consents, estimates, and copays, and directs structured clinical data into the EHR; its source-of-truth language supports an architecture where HealthLinc is a renderer and router rather than an intake database.
- Tebra/Kareo combines pre-appointment digital intake and e-signatures with mobile-browser delivery, staff review, record integration, reminders, and an iOS/Android practice-device kiosk, fitting independent practices that need graceful channel fallback.
- Epic MyChart eCheck-In and Welcome provide the enterprise reference for mobile demographics and insurance verification, copays, questionnaires, signatures, visible completion marks, and tablet continuation, including clinical questionnaire filing into configured EHR structures rather than a companion-app silo.

## Proposed feature set for openPractice

Parity floor: guided demographic and insurance confirmation, guarantor and subscriber capture, digital forms and e-signatures, appointment reminders, estimates, patient-initiated payments, and kiosk or desk completion. Adopted gaps: front/back insurance images with patient-confirmed OCR suggestions; administratively configured HIPAA notice, financial policy, and consent-to-treat equivalents with signer authority and versioned evidence; payment-method tokenization through processor adapters; estimate display and acknowledgment; appointment-type task assembly with due-before dates; encrypted partial-save and cross-device resume; push reminders with consented SMS fallback; one readiness state feeding the existing registration-readiness contract and front-desk worklists; and channel-neutral continuation from HealthLinc to kiosk or staff assistance. Twist: a previsit completion percentage with explainable blockers appears on staff worklists and can unlock bounded self-service steps, but never authorizes an autonomous financial action or treats a percentage as clinical readiness.

## Interfaces and boundaries

Consumes appointments and task rules from scheduling, administrative form definitions and versions from openPractice, coverage and relationship records, approved estimate and payment-tokenization intents, communication preferences, and verified identity and proxy authority. For health history, screenings, or EHR-configured questionnaires, HealthLinc consumes only openChart-authored task and rendering contracts and returns responses directly to openChart as versioned FHIR R4-preferred clinical content with provenance; it does not persist an authoritative copy in openPractice or itself. Emits administrative updates, signatures, acknowledgments, coverage images, processor tokens, completion events, and readiness blockers to openPractice, while clinical completion status may be referenced without disclosing answers. Kiosks and staff desks use the same task identifiers and ownership rules, and all payment execution remains patient-initiated or human-authorized.

## Alternatives and tensions

A unified checklist is easier for patients but can obscure which backend governs a form, so ownership and submission destination must remain machine-enforced even if the visual journey is seamless. Hard due dates improve preparation but cannot deny urgent or equitable access; overdue items need staff-visible exceptions. Cross-device resume aids completion yet expands sensitive-state exposure, favoring server-side owner storage and minimal encrypted device caches. Completion percentages motivate action but may oversimplify high-impact blockers, while payment methods and estimates can reduce friction only if acknowledgment is not misrepresented as consent to charge or a price guarantee.

## Open questions

- Which administrative artifacts may be partially saved in openPractice, and must partial clinical questionnaire responses be saved only through an openChart draft contract?
- How should one percentage weight blocking identity or coverage defects versus optional tasks without implying that administrative completion means clinical preparedness?
- Which form changes invalidate prior signatures or acknowledgments, and how should in-progress work migrate when appointment type, proxy authority, or estimate version changes?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [HealthLinc App Onboarding](openpractice-feature-list-healthlinc-app-onboarding.md), [AI Intake Assistance](openpractice-feature-list-ai-intake-assistance.md), [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md), [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md).
