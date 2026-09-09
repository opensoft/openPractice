# Registration And Demographics — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should turn registration into a guided, identity-safe path to a billing-ready administrative record, combining self-service capture with duplicate adjudication, relationship modeling, privacy controls, and explicit completion gates rather than accepting minimally populated demographics.
Topics: openpractice-feature-list, registration, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Guided registration intake** — adapt required steps to patient age, payer, guarantor arrangement, location, and appointment context while preserving staff override with reason.
- **Identity-safe record creation** — verify asserted identity, score likely duplicates, and route uncertain matches to a merge-adjudication queue instead of silently creating or combining records.
- **Coverage capture from cards** — acquire front/back images, extract candidate payer/member data with OCR, and require confirmation before structured fields become authoritative.
- **Relationship-aware billing identity** — model patient, subscriber, guarantor, legal representative, and communication recipient as distinct roles with effective dates and provenance.
- **Privacy-aware front desk** — enforce confidential-contact instructions, VIP/break-glass restrictions, aliases, and minimum-necessary display across search, kiosk, and work queues.
- **Billing-readiness gates** — expose missing or contradictory registration elements as actionable checks before eligibility, estimates, claims, or patient statements proceed.

## Focus

Which patient-administration capabilities make intake fast enough for self-service and front-desk use while producing an identity-safe, complete, and billing-ready record?

## Current state: OpenEMR baseline

OpenEMR supports new-patient search and creation, configurable demographic layouts, insurance entry, basic duplicate-oriented search, portal self-registration, and staff review of patient-submitted demographic changes. Its portal can collect preliminary demographics and insurance before arrival, but requiredness is largely form-driven; identity proofing, explainable probabilistic matching, governed merge adjudication, insurance-card OCR, relationship lifecycle controls, and downstream registration-completion gates are not a coherent native workflow. Kiosk-style capture and richer intake automation depend on configuration or modules.

Sources: OpenEMR Project Wiki, “HOWTO: Create a New Patient Record - OpenEMR v7”; OpenEMR Project Wiki, “HOWTO: Patient self-registration in the Native Patient Portal v6.1”; athenahealth Help, “Patient Registration”; eClinicalWorks, “healow CHECK-IN”; NextGen Healthcare, “Patient Registration” and “Management of Demographic Records.”

## Enterprise gap candidates

- athenahealth combines duplicate checking, registration audit history, insurance and guarantor workflows, family billing relationships, and portal registration across staff, kiosk, smartphone, and invitation paths; the lesson is continuity from intake into collections, not merely more fields.
- eClinicalWorks uses the healow ecosystem for previsit demographic and insurance review, consent capture, card/license scanning, payments, and contactless arrival, demonstrating broad self-service while leaving room for a less fragmented staff experience.
- AdvancedMD packages intake, consent forms, kiosk, portal, reminders, and payments as modular practice-management capabilities; modularity suits varied clinic maturity but can make completion state harder to reason about across add-ons.
- NextGen supports configurable required demographics, insurance and guarantor profiles, relationship roles, real-time eligibility entry points, history, and enterprise-wide person lookup, reflecting the needs of specialty and larger groups.
- Tebra/Kareo emphasizes an integrated front door for independent practices, where registration must connect simply to scheduling, billing, communications, and patient acquisition without enterprise implementation overhead.

## Proposed feature set for openPractice

Parity floor: configurable demographics and insurance intake, patient search, subscriber/guarantor capture, portal self-registration, staff review, and auditable updates. Adopted gaps: guided web, mobile, and kiosk flows; identity-verification adapters; explainable duplicate scoring with side-by-side merge adjudication and reversible provenance; insurance-card image capture with OCR suggestions; effective-dated patient/subscriber/guarantor/representative relationships; communication preferences; confidential-contact and VIP/break-glass flags; and configurable quality gates that publish readiness blockers to eligibility and billing work queues. Twist: a versioned registration-readiness contract explains exactly why a record is or is not usable for each downstream operation, so automation can request missing evidence but only authorized humans can resolve identity conflicts or approve merges.

## Interfaces and boundaries

Consumes appointment context from scheduling, payer and plan dictionaries from coverage, patient-supplied images and assertions from approved intake channels, and identity references from openChart or OpenEMR through FHIR R4 where available. Emits administrative Patient/RelatedPerson-compatible identity, relationship, preference, provenance, and readiness signals to coverage, billing, communications, and reporting. openPractice owns operational registration and financial-party relationships; it does not own the EHR's signed clinical facts, encounter record, medications, results, or orders, and MedxFactory receives only bounded capability-aware registration scopes.

## Alternatives and tensions

Strict completion gates improve clean-claim rates but can block urgent access or disadvantage patients lacking documents, so exception paths need reasons, expiry, and review rather than hard denial. Probabilistic matching improves detection but creates catastrophic merge risk; suggestions must remain non-destructive until adjudicated. Central person identity across locations reduces duplication yet raises privacy and organizational-boundary questions. OCR saves typing but card images are sensitive records requiring retention, access, and deletion policy.

## Open questions

- Which identity-verification levels are appropriate for remote self-registration, on-site kiosk use, and staff-assisted intake?
- Should demographic merges use one enterprise master identity or location-scoped records joined by explicit links?
- Which readiness failures block billing actions, which merely warn, and who can grant time-limited exceptions?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md), [Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md), [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md).
