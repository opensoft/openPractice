# Access Equity And Channel Parity — Brainstorm

Status: brainstorm
Kind: architecture
Summary: every beta workflow should preserve equivalent assisted, accessible, language-aware, and non-smartphone paths so self-service improves convenience without becoming a new condition for scheduling, registration, arrival, communication, or payment.
Topics: openpractice-small-practice-beta, access-equity, patient-engagement, scheduling, consent
Repository context: openPractice — cross-cutting access requirements for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Structured access needs** — record preferred language, interpreter need, communication accessibility, alternative format, relay, literacy, device, and assistance preferences with provenance and minimum-necessary visibility.
- **Channel capability matrix** — declare which actions each staff, telephone, web, kiosk, paper-assisted, SMS, email, or app channel can complete and its required identity assurance.
- **Equivalent fallback** — let staff resume the same task and evidence trail when a patient lacks a device, connectivity, documents, literacy, accessibility support, or confidence in self-service.
- **Interpreter and accommodation work** — route requested assistance, confirmation, preparation, and unresolved gaps with ownership and deadlines.
- **Authoritative translation control** — distinguish approved translated forms and notices from machine-translated guidance, preserving the authored version and language used for signature.
- **Equity observability** — compare completion, abandonment, delay, no-show, payment, and exception outcomes by permitted access dimensions without using them to deny service.

## Focus

Which shared product rules prevent digital registration, self-scheduling, communications, previsit tasks, and arrival automation from disadvantaging patients who cannot or choose not to use the preferred digital path?

## Proposed model

Channel parity does not require identical interfaces. It requires equivalent access to the underlying administrative outcome, clear differences in identity assurance, and one continuation identity so a task can move between channels without re-entry or lost evidence.

Each patient-facing task declares:

- purpose and owning domain;
- subject and representative authority;
- supported languages and authoritative content versions;
- required identity or reauthentication level;
- supported channels and accessibility characteristics;
- partial-save and continuation behavior;
- staff-assistance capability and minimum necessary context;
- failure, timeout, revocation, and escalation paths;
- whether the task blocks an operation, warns, or may receive a time-limited exception;
- completion and abandonment events suitable for equity review.

An **Access Need** is effective-dated and purpose-limited. It may identify a preferred language, qualified-interpreter request, large print or screen-reader requirement, hearing or speech relay, simplified instructions, proxy assistance, confidential-contact restriction, or non-digital preference. Sensitive needs appear only to roles and workflows that must arrange access; analytics receives governed dimensions or aggregates rather than a reusable patient profile.

Machine translation and read-aloud may help with navigation or approved explanatory text, but they do not replace validated translations or qualified assistance where authoritative forms, notices, consents, or clinical instruments require them. HHS describes language-assistance responsibilities for many federally funded health programs in its [Limited English Proficiency resources](https://www.hhs.gov/civil-rights/for-individuals/special-topics/limited-english-proficiency/index.html). Product capability should support applicable obligations while leaving legal applicability and local policy to qualified practice governance.

## Interfaces and boundaries

Consumes patient and representative preferences, identity and authority state, authored content versions, practice capabilities, channel health, accessibility metadata, and staff assistance decisions. Emits channel choices, accommodation work, continuation links, approved language and format selections, failure reasons, and privacy-safe outcome measures to registration, scheduling, communications, previsit, arrival, billing, and reporting.

It does not infer disability, language proficiency, immigration status, literacy, or financial hardship from behavior; use protected attributes for punitive prediction; require an app, location permission, Bluetooth, email, credit card, or internet access as a condition of ordinary service; or treat automated translation as authoritative legal or clinical content.

## Alternatives and tensions

Supporting every channel equally at launch would overwhelm a small beta, while choosing one preferred digital path risks making staff perform invisible re-entry for everyone else. A shared task and evidence contract lets channels vary without fragmenting state. Recording detailed access needs improves service but can create a sensitive profiling surface. Strict identity proofing protects records yet can exclude people with stale demographics or missing documents; staff-assisted verification needs bounded evidence and review rather than permanent bypass.

## Open questions

- Which channels must complete each beta task, and which may provide only notification or handoff?
- Which languages and accessibility scenarios must be tested with the design partner before beta acceptance?
- What identity and evidence permit staff-assisted recovery when a patient cannot pass the default remote proofing flow?
- Which incomplete tasks may never block access, and which require a reasoned, time-limited exception?
- How can outcome analysis identify channel barriers without exposing small cohorts or enabling discriminatory routing?

## Relationships

Combined with [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), and [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). It constrains the existing registration, scheduling, communications, previsit, AI, and presence concepts rather than existing as an optional patient-engagement add-on.
