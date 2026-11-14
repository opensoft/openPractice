# Referral Administration — Brainstorm

Status: brainstorm
Kind: process
Summary: openPractice should manage the administrative lifecycle of incoming and outgoing referrals from receipt through identity matching, evidence completion, coverage and authorization work, scheduling, status communication, and closure while the connected EHR retains clinical order and appropriateness authority.
Topics: openpractice-small-practice-beta, referral-administration, scheduling, coverage, routing
Repository context: openPractice — administrative referral workflow for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Multi-channel referral intake** — accept structured API events, secure messages, faxes, uploads, and staff entry into one traceable intake queue.
- **Identity-safe matching** — connect a referral to an existing administrative identity or route ambiguity for adjudication before creating another patient record.
- **Completion checklist** — show missing referral facts, administrative documents, coverage evidence, authorization state, and EHR-owned clinical references without treating all missing items as equal blockers.
- **Referral-to-appointment conversion** — carry source, requested offering, urgency supplied by the EHR, communication authority, and evidence into constrained scheduling without re-entry.
- **Closed-loop status** — return received, needs-information, scheduled, completed-reference-received, declined, cancelled, and closed states through the sender's supported channel.
- **Leakage and aging visibility** — measure referral age, contact attempts, conversion, abandonment, missing-information causes, and sender response without interpreting clinical outcomes.

## Focus

Which administrative capabilities let a small practice turn heterogeneous referrals into booked, financially prepared appointments without copying clinical orders into openPractice or leaving staff to reconcile fax folders, EHR queues, phone notes, and spreadsheets?

## Proposed model

A **Referral Case** represents the administrative work around a referral, not the clinical order itself. It records sender and receiver organizations, patient or candidate identity, direction, requested administrative service offering, EHR-owned order or ServiceRequest reference when available, received priority as an assertion from the clinical source, destination, requested time window, evidence manifest, communication authority, coverage and authorization links, work ownership, status history, and correlation identity.

A candidate incoming lifecycle is:

```text
received
  ├→ quarantined or invalid
  └→ identity-unmatched → matched
                         ├→ information-needed ↔ administratively-ready
                         ├→ unable-to-contact
                         └→ scheduling → scheduled → service-reference-received → closed
                                                ├→ cancelled
                                                └→ no-show / reschedule-needed
```

Outgoing cases reuse the same evidence and communication mechanics but reverse the organizational direction. A referral may close because the recipient acknowledged and scheduled it, because the patient declined, because information could not be completed, or because an authorized clinical source withdrew or replaced its order. Closure always carries a disposition and evidence; it is not equivalent to a clinical outcome.

The domain should distinguish four decisions:

1. identity match, owned by governed administrative identity adjudication;
2. administrative completeness, owned by openPractice rules and authorized staff;
3. clinical appropriateness or urgency, asserted and owned by the connected EHR or clinician;
4. appointment reservation, owned by scheduling under its constraints.

Fax or image intake may use OCR only to propose classifications and field values. A human confirms uncertain identity, destination, or consequential data before it becomes authoritative.

## Interfaces and boundaries

Consumes administrative identity, provider and location directories, service offerings, scheduling availability, coverage and authorization state, communication authority, document evidence, and versioned clinical order references from openChart or another EHR. Emits referral work, contact intents, appointment context, missing-information requests, status events, and non-clinical referral metrics.

It does not author, alter, interpret, prioritize, or cancel clinical orders; determine medical necessity; diagnose; select treatment; or maintain a second clinical referral record. Clinical documents received accidentally or intentionally are routed to the EHR owner under minimum-necessary policy, while openPractice retains only the administrative evidence and authorized transfer history it needs.

## Alternatives and tensions

Keeping referrals entirely in the EHR protects the clinical boundary but leaves financial clearance, communications, scheduling, and external-channel intake fragmented. Copying every referral field into practice management improves local search yet creates competing truth. A shared case with immutable EHR references and destination-aware documents preserves both concerns. Strict completeness gates reduce failed appointments but can delay access; blockers, warnings, urgent exceptions, time limits, and retrospective review must be separate. Closed-loop reporting improves accountability but external partners may only support fax or phone, requiring staff-confirmed status evidence.

## Open questions

- Is inbound specialty referral management required for the beta design partner, or should the first slice support only a basic referral reference on an appointment?
- Which referral statuses can be standardized across structured and manual channels without implying clinical meaning?
- Who may mark a referral administratively complete when clinical evidence remains EHR-owned or externally unavailable?
- How should duplicate referrals, replaced orders, patient choice among destinations, and multi-appointment referral series relate?
- Which sender-facing status responses may include patient information under the recorded relationship and purpose?

## Relationships

Combined with [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). It also consumes the existing [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md) and [Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md) capabilities.
