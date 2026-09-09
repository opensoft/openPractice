# Administrative Documents And Claims Attachments — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openPractice needs a governed evidence and correspondence layer that can receive, match, classify, route, sign, retain, disclose, and transmit administrative documents and claims attachments without becoming a second clinical chart.
Topics: openpractice-small-practice-beta, administrative-documents, interoperability, claims, doc-workflow
Repository context: openPractice — shared administrative evidence and claims-attachment capability for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Administrative evidence object** — give each document or external assertion an owner, purpose, provenance, integrity hash, sensitivity, retention class, and domain references.
- **Unmatched intake queue** — quarantine and route fax, upload, scan, secure-message, API, and partner documents that cannot yet be trusted or attached safely.
- **Destination-aware routing** — retain administrative artifacts in openPractice while sending EHR-owned clinical material to the connected EHR without creating an authoritative duplicate.
- **Versioned forms and signatures** — preserve signer identity, representative authority, content version, intent, signature method, timestamp, and revocation or supersession state.
- **Claims-attachment workflow** — turn requests for additional information into bounded evidence assembly, authorization, X12/HL7 packaging, transmission, acknowledgment, and deadline work.
- **Retention and disposition controls** — apply configurable minimum-necessary retention, legal hold, export, correction, and verified disposal with evidence.

## Focus

What common evidence mechanics are needed across registration, referrals, coverage, authorizations, coding review, claims, appeals, payments, privacy operations, and implementation without building a generic file dump or copying the clinical chart?

## Proposed model

An **Administrative Evidence Object** separates metadata and authority from binary storage. Its minimum envelope includes:

- evidence identifier and version;
- owning domain and business purpose;
- patient, representative, provider, referral, authorization, charge, claim, payment, incident, or implementation references as authorized;
- source channel, sender assertion, received timestamp, custodian, and correlation identity;
- document or evidence type and structured extraction state;
- content location, cryptographic digest, malware-scan state, encryption and key reference;
- signer, representative authority, signature method, signed content version, and verification evidence where applicable;
- sensitivity, minimum-necessary access policy, retention class, disposition date, legal hold, and disclosure history;
- lifecycle and every match, classification, replacement, correction, transmission, or disposal decision.

A generic intake lifecycle is:

```text
received → quarantined → scanned → classified
                              ├→ unmatched → human-adjudicated
                              └→ matched → routed → accepted / rejected / superseded
                                                       └→ retained / held / disposed
```

OCR, document classification, and identity matching produce suggestions with confidence and source coordinates. They do not silently attach a document to a patient, claim, or referral when the match is uncertain.

Claims attachments require a more specific transaction path. A request for additional information becomes a domain event and deadline; authorized staff select the requested evidence or ask the EHR for a purpose-bounded release package; the connector validates the manifest, signature, and applicable X12/HL7 envelope; transmission and acknowledgments join the claim history; and minimum necessary retained evidence supports later dispute or audit. The clinical source remains authoritative even if openPractice must transiently process or retain a submission copy under policy.

CMS finalized X12N 275 and 277 Version 6020 transactions, selected HL7 attachment implementation guides, and electronic-signature requirements for claims attachments in 2026. Its [final-rule fact sheet](https://www.cms.gov/newsroom/fact-sheets/administrative-simplification-adoption-standards-health-care-claims-attachments-transactions) states an effective date of May 26, 2026 and compliance 24 months later. This makes attachments a planned claims and interoperability capability rather than an unspecified future connector option. Prior-authorization attachment standards were not finalized in that same rule, so claims and authorization attachment policies must remain versioned separately.

## Interfaces and boundaries

Consumes files or structured evidence from patients, staff, partners, payer channels, processors, and bounded EHR release contracts. Emits verified metadata, routed evidence, signature records, transfer manifests, disclosure events, attachment transactions, acknowledgments, work items, and retention decisions to owning domains.

The layer does not decide clinical meaning, rewrite signed content, infer representative authority from document possession, retain all incoming material forever, or expose binary content merely because a user can see the related patient. It is not a clinical document repository, general enterprise content-management system, or ungoverned object store. Raw payment credentials, secrets, and unnecessary identity evidence are excluded.

## Alternatives and tensions

One central document service improves scanning, access, retention, and provenance, but broad metadata or search can become a second PHI surface. Domain-specific stores reduce blast radius yet duplicate matching, malware scanning, signature, and retention behavior. Retaining the exact transmitted attachment aids disputes, while minimizing clinical duplication favors a manifest, hash, and source reference; policy and partner requirements may justify different retention classes. Fax fallback supports real partners but weakens structure and acknowledgment certainty.

## Open questions

- Which document and evidence types must openPractice retain authoritatively, relay, reference, or reject in the beta?
- What is the portable default for encrypted binary storage and integrity anchoring on a standard Frappe bench?
- Which identity-match confidence levels require human adjudication before routing?
- When a claim requires clinical material, should openPractice retain the transmitted package, a redacted copy, or only a signed manifest and EHR source reference?
- Which electronic-signature methods and evidence profiles are needed for administrative forms versus regulated claims attachments?

## Relationships

Combined with [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). It combines with [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md) and [Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md) in [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md).
