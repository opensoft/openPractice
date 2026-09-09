# openPractice Small-Practice Beta Feature Analysis Overview — Brainstorm

Status: brainstorm
Kind: reference
Summary: the first openPractice beta should serve one small ambulatory practice through a thin but complete access-to-financial-close loop, retaining the existing fifteen-domain research while adding the missing practice-administration foundations and treating native-app, AI, prediction, and sensor ideas as layered later capabilities.
Topics: openpractice-small-practice-beta, practice-strategy, feature-roadmap, practice-administration, revenue-integrity
Repository context: openPractice — whole-product analysis and feature framing for the first small group practice or small clinic beta
Captured: 2026-08-26

## Possible feats

- **Small-practice operating core** — configure one practice and run registration, scheduling, coverage, charging, claims, remittance, patient billing, payment, work, and reconciliation as one supported product.
- **Practice administration as a first-class cluster** — add organization and service structure, provider enrollment and directory readiness, referral administration, document evidence, equitable channels, and governed implementation.
- **Revenue integrity through financial close** — join claim attachments, contract expectations, remittance variance, settlement, credit and refund control, and balanced accounting export to the existing revenue-cycle provenance chain.
- **Layered patient experience** — let responsive web, kiosk, staff assistance, and a future companion app render tasks from authoritative domains without becoming new sources of truth.
- **Prerequisite-led innovation** — introduce AI, prediction, and presence sensing only after deterministic workflows, consent, corrections, evidence, metrics, and evaluation data exist.

## Motivation

The existing [openPractice Feature List Overview](openpractice-feature-list-overview.md) is a strong strategic research packet. It defines a trustworthy administrative and financial product boundary, identifies useful competitive gaps, and proposes differentiated provenance, communications, metrics, and compliance behavior. It is intentionally broad: fifteen atomic domains contain 96 candidate feats and preserve 63 open questions.

That breadth does not yet describe a beta. Product domains, platform controls, delivery channels, and experimental enhancements appear at the same level. Four front-office atomics center the companion app, mobile previsit experience, AI intake, or sensor-assisted arrival, while the product lacks a coherent practice-administration cluster for organization structure, provider enrollment, referrals, administrative documents, payer contracts, reconciliation, accounting exchange, and implementation.

The selected first-beta customer resolves an important ambiguity: a small group practice and a small clinic are one launch archetype for planning purposes. This packet calls it a **small ambulatory practice** and uses its role compression, limited IT capacity, workflow breadth, and need for understandable exceptions to reshape the feature set. It does not silently promote the analysis beyond brainstorm status.

## Goals

- A small practice can configure its organization, providers, locations, offerings, roles, core policies, and first connectors without an enterprise implementation project.
- Staff can register, schedule, financially prepare, check in, charge, bill, post, collect, reconcile, and account for one patient journey without duplicate entry or database intervention.
- Every material appointment, claim, document, payment, override, correction, and export identifies its authoritative owner, evidence, actor, version, and correlation path.
- Ordinary partner and patient-channel failures create visible, owned work with assisted fallback rather than silent loss or a dead end.
- A patient can complete the administrative outcome without owning a smartphone, granting location access, speaking English, possessing every document, or using an electronic payment method.
- The beta distinguishes included behavior, governed manual behavior, and explicitly deferred capabilities so success is not measured by an unbounded feature list.

## Non-goals

This packet does not ratify a roadmap, create implementation tasks, select vendors, promise a date, or replace an OpenSpec proposal and Speckit feature. It does not turn openPractice into an EHR, general ERP, payroll system, bank, clearinghouse network, billing bureau, legal advisor, contract-negotiation agent, or hospital-inpatient revenue platform.

The default beta hypothesis excludes broad connector catalogs, enterprise organization hierarchies, multi-practice billing-service tenancy, FQHC-specific compliance packages unless the design partner needs them, generated AI answers, learned prediction, autonomous coding, lobby beacon infrastructure, and autonomous consequential financial or patient-impacting action. Deferral preserves these ideas and their research; it does not reject them.

## What the system delivers

The packet preserves the eleven durable domains and shared services in the current research: registration, scheduling, communications, coverage and authorizations, charge capture, claims and remittance, patient billing, operations work, reporting, platform security, and interoperability. It reclassifies companion-app onboarding, cross-backend previsit presentation, AI assistance, and sensor-assisted arrival as experiences or enhancements built over those owners.

It proposes the missing capabilities most likely to matter to the first small practice:

- effective-dated organization, legal and billing entity, location, provider, resource, and administrative service-offering structure;
- provider credential evidence, payer enrollment cases, revalidation, participation readiness, and directory attestations;
- incoming and outgoing referral administration joined to identity, documents, coverage, authorization, scheduling, and external status;
- administrative evidence, correspondence, forms, signatures, unmatched intake, retention, and claims attachments;
- payer agreement, reimbursement expectation, remittance variance, and underpayment work;
- balanced healthcare subledger, deposit and settlement matching, unapplied cash, credits, refunds, period close, and accounting export;
- guided configuration, migration rehearsal, policy simulation, cutover, stabilization, and rollback;
- structured language, accessibility, accommodation, assisted-channel, and non-smartphone parity.

Three time-sensitive refinements belong in later updates to the original feature packet: CMS finalized claims-attachment standards in 2026; impacted-payer prior-authorization operational and API requirements span 2026 and generally 2027; and patient estimates need an explicit uninsured or self-pay Good Faith Estimate and dispute-evidence path. These findings are recorded in [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md) and [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md).

## System model

```text
patients and representatives                         referral sources
          │                                                  │
          ▼                                                  ▼
staff · telephone · responsive web · kiosk · future app · fax/API
          │                                                  │
          └──────────────────────┬───────────────────────────┘
                                 ▼
                    PRACTICE ADMINISTRATION
         structure · providers · enrollment · referrals · evidence
              configuration · migration · equitable access
                                 │
                 ┌───────────────┴────────────────┐
                 ▼                                ▼
        FRONT OFFICE AND ACCESS            REVENUE CYCLE
     identity · schedule · comms        coverage/auth · charges
        previsit · arrival              claims/remit · patient AR
                 │                                │
                 └───────────────┬────────────────┘
                                 ▼
                  REVENUE INTEGRITY AND CLOSE
           attachments · contract expectation · variance
             settlements · subledger · accounting export
                                 │
                                 ▼
                    OPERATIONS AND INTELLIGENCE
                 owned work · SLAs · versioned metrics
                                 │
                                 ▼
                         PLATFORM AND TRUST
       authority · audit · retention · recovery · FHIR/X12/API

clinical order, fact, result, medication and signed-note authority
remain in openChart or another connected EHR across a versioned boundary
```

## Cluster map

- [Synthesis: Small-Practice Product Shape](openpractice-small-practice-beta-synthesis-product-shape.md) — joins the launch archetype, normalized catalog, beta finish line, and default deferral policy.
- [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md) — connects practice structure, provider readiness, referrals, evidence, implementation, and equitable channels into one administrative-readiness graph.
- [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md) — connects claim evidence, expected reimbursement, actual adjudication, settlement, allocation, correction, and accounting export.

## How it fits

This packet supplements rather than replaces the existing feature-list packet. The earlier atomics remain the competitive and design-space source for each capability; [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md) supplies a beta-oriented reading and a common feature contract. Future staged features should link to both packets instead of copying their prose.

The [openPractice Constitution](../../.specify/memory/constitution.md) remains authoritative for HIPAA-by-design safeguards, human financial and patient-impacting authority, test-first development, clinical ownership, versioned integration contracts, and deployment portability. This packet applies those constraints to product shape but does not amend them. Current legal and standards applicability remains subject to qualified review and versioned control tracking.

The existing cluster graph gains two lenses rather than a competing hierarchy:

- practice administration supplies valid organization, provider, referral, evidence, and implementation context to front office, revenue cycle, operations, and trust;
- revenue integrity and close extends the existing revenue cycle beyond remittance posting into expected reimbursement, settlement, correction, and accounting reconciliation.

The default sequence is foundation, patient access and financial preparation, a complete professional revenue loop, then exception readiness. Responsive and staff-assisted channels establish the baseline. Companion-app federation, AI, prediction, and presence sensing enter only when the owning workflows and evaluation evidence can support them.

## Key decisions and open questions

Captured direction:

- A small group practice and small clinic are one first-beta archetype.
- The connected EHR owns clinical facts and clinical decisions; openPractice owns administrative and financial state.
- The beta should favor coherent end-to-end operation, progressive disclosure, explainable exceptions, bounded authority, and manual-safe fallbacks.
- New practice-administration and financial-close capabilities are necessary to balance the current patient-channel and revenue-cycle research.

Strong working hypotheses that still need explicit product decisions:

- US-first operation, inferred from HIPAA, CPT, X12, US Core, and current payer scope;
- one practice per Frappe site and one bounded connector per external capability class for the earliest beta;
- a complete professional registration-to-accounting slice rather than an access-only prototype;
- responsive web and assisted patient workflows in beta, with the native companion app not required by default;
- AI, predictive models, and geofence or Bluetooth sensing after beta foundations.

Open decisions:

- What provider, location, appointment, claim-volume, and staff envelope defines beta acceptance?
- Which specialty and design partner validate the archetype?
- Which EHR, clearinghouse, payer transaction set, processor, communication vendor, and accounting destination form the first connector suite?
- Which referral, authorization, provider-enrollment, contract, and claims-attachment functions must be automated in beta versus represented as governed manual work?
- Does the first external milestone require live production payer exchange, or may certified test traffic precede it?
- Which companion-app strategy and product name survive identity-architecture and trademark review?
- When should a community-health/FQHC pack add sliding-fee, UDS, enabling-service, and program-governance behavior?

## Document map

Overview:

- this document

Syntheses:

- [Synthesis: Small-Practice Product Shape](openpractice-small-practice-beta-synthesis-product-shape.md)
- [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md)
- [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md)

Atomics — product framing:

- [Small Ambulatory Practice Beta Archetype](openpractice-small-practice-beta-launch-archetype.md)
- [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md)
- [Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)

Atomics — practice administration:

- [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md)
- [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md)
- [Referral Administration](openpractice-small-practice-beta-referral-administration.md)
- [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md)
- [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md)
- [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md)

Atomics — revenue integrity:

- [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md)
- [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md)
- [Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md)
