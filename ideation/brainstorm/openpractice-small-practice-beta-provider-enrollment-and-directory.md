# Provider Credentialing, Enrollment, And Directory Lifecycle — Brainstorm

Status: brainstorm
Kind: process
Summary: openPractice should turn provider credentials, affiliations, payer enrollments, revalidations, and directory attestations into one evidence-backed administrative-readiness lifecycle whose expiries and gaps prevent avoidable scheduling and billing failures.
Topics: openpractice-small-practice-beta, provider-enrollment, operations, coverage, practice-structure
Repository context: openPractice — provider administrative readiness for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Provider evidence inventory** — track licenses, certifications, insurance, identifiers, attestations, affiliations, and supporting documents with issuer and expiry provenance.
- **Payer enrollment cases** — manage one case per provider, entity, location, payer, product, and participation purpose from preparation through approval or denial.
- **Revalidation and expiry watch** — generate accountable work before licenses, attestations, enrollments, or delegated relationships lapse.
- **Directory accuracy campaigns** — require effective-dated review of provider, location, contact, specialty, and accepting-new-patients assertions and retain the attesting actor.
- **Administrative readiness contract** — explain whether a provider may be scheduled, rendered on a claim, listed publicly, or used for a payer product on a service date.
- **Safe delegation** — let authorized practice staff prepare data and evidence without capturing external portal passwords or forging the provider's required attestation.

## Focus

How can a small practice know whether each provider is administratively ready for a particular location, payer, service, and date without reducing credentialing and enrollment to reminder dates in a general task list?

## Proposed model

The source state belongs in a provider-administration domain; operational queues receive the work that state produces. A compact model includes:

- **Provider Administrative Profile** — identifiers, taxonomies, names, contact details, practice affiliations, and evidence ownership;
- **Credential Evidence** — type, issuer, jurisdiction, effective and expiry dates, verification method, document reference, and review state;
- **Practice Affiliation** — provider-to-entity, location, offering, and role relationship over time;
- **Enrollment Case** — payer or program, product, entity, provider, location, purpose, submission channel, external reference, status, dates, requests, correspondence, and decision;
- **Directory Assertion** — the public or payer-facing fact, its source, last verification, destination, discrepancy state, and attesting authority;
- **Readiness Evaluation** — a dated, explainable result for scheduling, claim rendering, ordering or referring use, directory publication, or another administrative action.

A candidate enrollment lifecycle is:

```text
draft → evidence-needed → ready-for-attestation → submitted → pending
                                              ├→ more-information-requested
                                              ├→ approved → revalidation-due → renewed
                                              └→ denied / withdrawn / expired
```

Transitions that represent an external commitment require the correct human authority. Automation may prepopulate known facts, detect discrepancies, assemble an evidence checklist, watch status, and route follow-up; it must not sign an attestation or submit with a person's authority unless the external system and recorded delegation explicitly permit that act.

CMS describes PECOS as supporting provider and supplier enrollment, review, supporting-document upload, and electronic submission, with distinct application paths for clinics, groups, individuals, and ordering or certifying practitioners in its [enrollment guidance](https://www.cms.gov/medicare/enrollment-renewal/providers-suppliers/chain-ownership-system-pecos/enrollment-applications). That external process is one adapter target, not the internal state model. Commercial payer and state-program processes need replaceable manual, portal-assisted, document, and future API channels.

## Interfaces and boundaries

Consumes provider and organization structure, verified evidence, payer and product dictionaries, external status and correspondence, and human attestations. Emits administrative readiness, expiration work, enrollment identifiers, directory assertions, and historical participation context to scheduling, coverage, charge capture, claims, communications, and reporting.

It does not make a legal credentialing determination, replace a payer or governing body, store uncontrolled copies of sensitive identity data, manage clinical privileging, manage general HR performance, or retain third-party portal passwords. External identifiers and evidence are minimum necessary, field-protected, access-limited, and subject to retention policy.

## Alternatives and tensions

Treating credentialing, enrollment, and directory data as separate modules mirrors external organizations but repeats the same provider facts and creates drift. One shared profile reduces duplication but must retain destination-specific assertions and disagreement. Blocking scheduling or claims on every stale field protects revenue yet may halt valid work because an external system is late; warning, hard-block, override, expiry, and retrospective review policies need distinct configuration. Automating portal work can save time while increasing credential and impersonation risk.

## Open questions

- Which credentials and payer enrollments are required for the first beta specialty and geography?
- Which readiness failures warn, block scheduling, block claim creation, or require time-limited override?
- Does beta support active submission adapters, or evidence checklists and manually recorded external status first?
- Which provider-directory destinations and verification cadences matter to the design partner?
- Who may attest, delegate preparation, approve a discrepancy, and review an expired-evidence override in a very small staff?

## Relationships

Combined with [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). Payer participation and effective dates also feed [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md).
