# Synthesis: Small-Practice Administration — Brainstorm

Status: brainstorm
Kind: architecture
Summary: practice structure, provider readiness, referrals, administrative evidence, equitable channels, and governed implementation form the missing practice-administration cluster that lets a small clinic configure itself once and turn external work into safe scheduling and billing context.
Topics: openpractice-small-practice-beta, practice-administration, practice-structure, provider-enrollment, referral-administration, administrative-documents, access-equity, implementation-lifecycle, synthesis
Repository context: openPractice — organization, provider, intake, evidence, access, and implementation operations for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **One administrative readiness graph** — explain whether the practice, provider, location, service, referral, and evidence are usable for a specific appointment or claim date.
- **Referral-to-ready appointment** — convert structured or manual intake into an identity-safe, documented, coverage-aware reservation without duplicating clinical orders.
- **Configuration as operational evidence** — prove which organization, authority, mapping, policy, and connector version produced each decision.
- **Small-team implementation control** — guide setup and change while keeping roles, approvals, reconciliation, and rollback visible to a practice without dedicated IT.
- **Equal outcome across channels** — let self-service, assisted, telephone, web, kiosk, and paper-supported paths act on the same tasks and evidence.

## Members and their joints

Atomic members: [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), [Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md).

```text
versioned practice structure and offerings
              │
              ├── provider evidence, enrollment and directory readiness
              │
external work ├── referral case ──▶ coverage/auth ──▶ scheduling
              │        │
              │        └── administrative evidence and EHR references
              │
              └── accessible self-service or accountable staff assistance

configuration, migration, audit and work queues surround every transition
```

### Structure ↔ provider readiness

A provider is not globally ready or unready. Readiness depends on the legal or billing entity, location, payer product, administrative service offering, role, and service date. The practice graph supplies those dimensions; credential and enrollment evidence explains whether a particular action is safe. Scheduling and claims consume the result instead of reconstructing it from expiration reminders.

### Referral ↔ evidence ↔ scheduling

A referral case is useful only when identity and evidence can be trusted and missing work is visible. The document layer receives and classifies material, routes clinical content to the EHR owner, and exposes administrative completeness. Coverage and authorization work attach through references. Scheduling creates the reservation only under its own constraints, while referral status returns through the sender's available channel.

### Implementation ↔ configuration

Implementation creates the first versions of organization structure, provider relationships, offerings, roles, mappings, and policies. Later administration uses the same version, validation, simulation, promotion, and evidence mechanics rather than treating go-live as a one-time exception. Migration rejects and unresolved identity or financial conflicts become normal accountable work instead of hidden cutover spreadsheets.

### Access needs ↔ every patient path

Language, accessibility, identity-recovery, device, proxy, and assisted-service needs affect referral contact, registration, scheduling, forms, arrival, and billing. Recording them only inside the companion app would make self-service the privileged path. A shared task contract preserves the outcome and evidence when staff or another channel assists.

### Work queues ↔ source ownership

Credential expiries, unmatched faxes, incomplete referrals, configuration failures, and accommodation requests all generate operations work, but the queue does not become their source of truth. Completion evidence updates the owning domain through a governed transition, and reopening preserves why the earlier disposition no longer holds.

## Emergent behavior

The cluster lets a small practice answer a deceptively difficult question before an appointment or claim: is the organization, provider, location, offering, patient path, referral, and evidence administratively ready for this action on this date, and if not, who owns the next step? That shared answer reduces re-entry, avoids preventable billing failures, and makes implementation state part of normal operations.

## Tensions to hold

Small-practice setup must be short, yet financial and authorization correctness require distinct legal, provider, location, and service relationships. Central evidence mechanics simplify intake while increasing the sensitivity of metadata and search. Hard readiness blocks protect cash flow but can obstruct access when external systems are slow or evidence is stale. Manual channels preserve equity but can become invisible labor. Templates should reduce effort without silently granting broad permissions or assuming one specialty's workflow.

## Recombination opportunities

The cluster feeds the existing [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md) with practice, provider, referral, and channel-readiness context. Provider participation and evidence feed [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md). Configuration provenance and evidence retention recombine with the existing [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md), while generated work and readiness metrics recombine with [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md).

## Open questions

- Which readiness dimensions must be evaluated synchronously during booking or claim creation, and which may become follow-up work?
- Which documents can openPractice retain, and which must be relayed to or referenced from the EHR?
- How much provider enrollment and referral automation is necessary for the beta design partner versus governed manual status?
- Which practice template produces a fast setup without hiding entity, provider, payer, and merchant distinctions?
- How should access exceptions expire and receive retrospective review when the same staff member performs several roles?
