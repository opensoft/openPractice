# AI Intake Assistance — Brainstorm

Status: brainstorm
Kind: report
Summary: HealthLinc should offer a governed intake copilot that explains paperwork, proposes explicitly confirmed answers, and resolves grounded administrative questions while preserving patient authority, routing clinical concerns to openChart, and leaving every response in its owning system with auditable AI provenance.
Topics: openpractice-feature-list, ai-intake, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Question-by-question interpreter** — an on-demand explanation says what a prompt means and why the practice asks it, using approved plain language without changing the authored question.
- **Confirmable answer drafts** — previously verified demographics, coverage, and other eligible facts can become field-level suggestions, but each remains visibly pending until the patient accepts or edits it.
- **Pre-submission completeness coach** — deterministic rules and bounded AI highlight unanswered required fields, conflicting dates, and likely omissions without diagnosing, rewriting clinical meaning, or submitting on the patient's behalf.
- **Cited administrative concierge** — free-text answers about hours, parking, arrival instructions, accepted insurance, what to bring, and cost-estimate basics come only from curated practice material and show the supporting source.
- **Clinical-boundary router** — symptom, medication, result, treatment, and other clinical questions receive a clear non-answer plus a route into an openChart-owned channel or appropriately authorized staff queue.
- **Accessible multilingual intake** — translated guidance, language-preserving escalation, and read-aloud controls help patients complete forms without treating machine translation as authority over authored consent or clinical wording.
- **Warm front-desk handoff** — a patient can move from copilot to live conversation with context preserved minimally and an openPractice operations item created for accountable follow-up.
- **Per-interaction decision record** — every question, answer, citation, disposition, model and prompt-policy version, and field provenance remains inspectable beside the submitted form.

## Focus

How can the built-in copilot in HealthLinc, the owner's working name for the ecosystem patient app, reduce paperwork confusion without silently filling fields, answering clinical questions, obscuring system ownership, or turning assistance into autonomous patient-impacting action?

## Current state: OpenEMR baseline

OpenEMR's native patient portal supports self-registration, demographic and insurance entry, document templates, questionnaires that patients may save and submit, and staff-facing portal-audit processing. Required-field highlighting and template substitution provide conventional form assistance, but the reviewed materials do not describe an embedded conversational copilot that explains individual prompts, drafts separately confirmed field answers, cites a curated administrative knowledge base, records model provenance, or enforces a dual-backend clinical-routing boundary. Its portal workflow demonstrates a useful human-review baseline: submitted documents enter a staff processing path rather than becoming silently authoritative.

Sources: OpenEMR Patient Self-Registration, Questionnaire Use in Portal, Patient Portal Document Templates, and Portal Audits wikis; Phreesia AI-Powered Intake and VoiceAI product pages; HHS Guidance on HIPAA & Cloud Computing, Business Associates guidance, and Minimum Necessary Requirement guidance.

## Enterprise gap candidates

- Phreesia-class intake products demonstrate demand for mobile and in-office registration, logic-driven question selection, EHR/PM routing, multilingual conversational handling, escalation, and time-stamped interaction records; openPractice can adopt the capability class without importing a vendor's automation assumptions.
- Personalized assistance should distinguish authored form logic from generated explanation: the former controls what is asked, while the latter may clarify only within approved policy and cited source material.
- Cross-system intake needs destination-aware orchestration because an administrative coverage update belongs in openPractice while an EHR-authored instrument and its response belong in openChart even when HealthLinc presents one coherent experience.
- A governed knowledge workspace can give practices publishing, approval, effective-date, localization, and expiry controls so administrative answers fail closed when their evidence is missing or stale.
- HHS treats a cloud processor that creates, receives, maintains, or transmits ePHI on behalf of a covered entity or business associate as a business associate, making BAA coverage, subcontractor controls, risk analysis, and minimum-necessary disclosure product requirements rather than optional procurement notes.

## Proposed feature set for openPractice

Parity floor: mobile-friendly registration and questionnaire delivery, save-and-resume, required-field validation, multilingual labels, accessible read-aloud, secure submission, staff review, and destination-aware transfer to the owning backend. Adopted gaps: on-demand plain-language explanations; per-field suggestions sourced only from previously verified eligible data with explicit patient confirmation and no silent autofill; gap and inconsistency coaching before submission; cited answers restricted to approved administrative knowledge; hard refusal and safe routing for clinical questions; live front-desk handoff with an operations queue item; model, policy, source, and response provenance; visible differentiation among patient-entered, imported, and AI-suggested values; minimum-necessary prompts; BAA-bound or locally hosted processors; contractual prohibition on training with PHI; transcript retention limits; and a documented constitutional security-review and risk-analysis gate before any PHI-touching release. HealthLinc may assist with openPractice-owned administrative forms and EHR-authored instruments delivered through versioned FHIR R4-preferred openChart contracts, but response data always lands in the authoring system. Twist: every AI exchange creates a compact decision record linking the patient's question, bounded answer, citations, disposition, confirmation state, and model/version evidence to the eventual form submission.

## Interfaces and boundaries

Consumes: authored field definitions and ownership metadata, previously verified facts explicitly eligible for suggestion, practice-approved administrative knowledge, language and accessibility preferences, versioned openChart instrument contracts, and patient confirmations. Emits: confirmed administrative responses to openPractice, confirmed EHR-instrument responses to openChart through its versioned contract, front-desk work items, clinical-route requests without generated clinical content, and integrity-protected decision records. Owns assistance policy, administrative knowledge governance, provenance, and handoff orchestration; does not diagnose, interpret symptoms or results, advise on medications, author clinical facts, alter an EHR instrument's meaning, submit any field without patient action, or bypass either backend's authorization and audit controls.

## Alternatives and tensions

Deterministic help text is easier to validate than generated explanation but cannot answer varied patient phrasing; retrieval-grounded generation may improve comprehension only if unsupported answers refuse cleanly and citations remain visible. Reusing verified data reduces burden yet can perpetuate stale errors, so suggestions must expose source and verification date and require field-by-field confirmation. Storing full transcripts helps investigation but expands the PHI surface; purpose-limited decision records, short transcript retention, redaction, and access controls should preserve evidence without retaining conversational exhaust. Local models improve deployment control but raise operating and quality burdens, while hosted models require BAAs, downstream-subprocessor review, no-training terms, and portable adapters. Translation and read-aloud improve access but cannot substitute for validated authoritative translations of consents or clinical instruments.

## Open questions

- Which fields are safe to suggest from verified history, and which should always require fresh patient entry because context, consent, or sensitivity changes?
- What confidence, citation, and freshness rules force an administrative answer to refuse and create a front-desk work item instead?
- How should decision records remain useful for disputes and quality review while transcript retention stays minimum necessary and jurisdictionally configurable?
- Which FHIR R4 Questionnaire and QuestionnaireResponse profiles, extensions, and provenance elements form the first openChart instrument contract?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Mobile Registration And Previsit Completion](openpractice-feature-list-mobile-registration-and-previsit.md), [HealthLinc App Onboarding](openpractice-feature-list-healthlinc-app-onboarding.md), [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md).
