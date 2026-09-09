# HealthLinc App Onboarding — Brainstorm

Status: brainstorm
Kind: report
Summary: HealthLinc, the owner's working name for the ecosystem-built patient companion, should turn appointment-aware outreach into a measurable install-to-value funnel while linking one strongly verified patient identity across openPractice and openChart without treating possession of a phone as authority to see a record.
Topics: openpractice-feature-list, healthlinc-app, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Appointment-aware adoption campaigns** — send personalized SMS invitations tied to an upcoming appointment, route each recipient to the correct iOS or Android store, and preserve campaign and appointment context without exposing sensitive detail in the message.
- **Identity-assured account linking** — redeem short-lived invite codes only after name, date-of-birth, and policy matching or documented staff assistance, so a reachable phone number can start but never complete record access.
- **Task-specific activation** — deep-link a newly active user to the exact pending action, such as finishing intake for appointment Y or reviewing and choosing to pay balance Z, with authorization rechecked after authentication.
- **Progressive permission sequencing** — request push notifications after their benefit is demonstrated, then ask for location or Bluetooth only at a feature moment that explains purpose, duration, fallback, and revocation.
- **Governed family profiles** — let one device switch among patient and proxy-authorized profiles while making subject, representative authority, expiration, and backend identity visible before every action.
- **Exportable adoption funnel** — measure sent, delivered, store-opened, installed, identity-linked, and first-task-completed stages by campaign and cohort, with privacy-safe exports for operational reporting.

## Focus

Which onboarding capabilities can convert an appointment-triggered invitation into verified, useful HealthLinc adoption without weakening identity assurance or creating separate identities across the administrative and clinical backends?

## Current state: OpenEMR baseline

OpenEMR's native patient portal supports browser-based self-registration, email verification, one-time credential setup, demographics and insurance capture, and separate portal accounts where people share contact details. It establishes a useful secure-web floor, but it does not present a first-party native-app adoption funnel spanning personalized SMS, operating-system store routing, install attribution, invitation-to-record linking, progressive device permissions, channel migration, and first-task completion. Nor does the baseline define one verified identity consumed through separate practice-operations and EHR contracts.

Sources: OpenEMR Project Wiki, “HOWTO: Patient self-registration in the Native Patient Portal v6.1”; athenahealth, “athenaPatient App” and “athenaPatient FAQs”; eClinicalWorks/healow, “Check-In” and “Have a question?”; Tebra Help Center, “Patient Experience: Patient Portal”; Epic, “Explore | MyChart is Epic.”

## Enterprise gap candidates

- athenahealth's athenaPatient app uses an already completed portal registration, offers biometric re-entry, appointments, self-scheduling, and enabled self-check-in, while athenaOne supports email, smartphone, kiosk, and in-office portal invitations; the opportunity is to measure and govern the whole invitation-to-first-value path rather than only account availability.
- eClinicalWorks pairs the iOS/Android healow app with existing portal credentials, PIN or biometric access, multiple practice and family records, and healow CHECK-IN reminders delivered by secure text and app; this demonstrates breadth but also the importance of explicit subject and authority cues when profiles converge on one device.
- AdvancedMD emphasizes portal intake and in-office iPad kiosk continuity for demographics, forms, signatures, and payments, showing why app adoption cannot become a prerequisite for completing an administrative task.
- NextGen Patient Engage combines actionable text outreach, mobile intake, and appointment-linked workflows, while its self-scheduling supports text and web entry without login; HealthLinc can preserve low-friction entry while reserving record disclosure and consequential actions for verified sessions.
- Tebra/Kareo activates portal access by invitation plus date-of-birth verification and supports authorized guests, mobile-browser access, messages, and payments; its model highlights proxy utility but leaves room for stronger effective-dated authority and cross-backend identity evidence.
- Epic MyChart provides the enterprise reference for native mobile access, eCheck-In, questionnaires, push-triggered work, MyChart Central identity, and purpose-timed arrival features; HealthLinc should borrow the connected experience pattern without collapsing clinical and administrative ownership into the app.

## Proposed feature set for openPractice

Parity floor: patient invitations, iOS/Android routing, secure account activation, appointments, task notifications, proxy access, and basic delivery and activation reporting. Adopted gaps: appointment-context SMS campaigns; privacy-preserving attribution; expiring invite codes; name, date-of-birth, and policy verification with throttling and staff-assisted exception evidence; one verified identity reference accepted by versioned openPractice and openChart contracts; staged push opt-in; migration of eligible reminders to the app while retaining consented SMS fallback; authenticated deep links to pending intake or patient-initiated payment choices; effective-dated multi-profile proxy controls; and just-in-time location or Bluetooth consent with non-device fallback. Twist: adoption funnel analytics become first-class reporting exports from sent through installed, linked, and first-task-completed, with denominator definitions, cohort filters, failure reasons, and no clinical content in event payloads.

## Interfaces and boundaries

Consumes upcoming appointment context, communication consent and channel preference, representative relationships, identity-match evidence, and bounded task descriptors from openPractice, plus clinical task descriptors and patient-facing health content only through versioned FHIR R4-preferred openChart contracts. Emits verified identity-link events for both backends, preference changes, campaign outcomes, device-token lifecycle, deep-link completions, and audit evidence to their owning domains. openPractice owns administrative identity, appointments, payments, and outreach operations; openChart owns all health information and clinical access decisions. HealthLinc renders and synchronizes across both governed contracts, keeps only encrypted purpose-limited transient state, and never creates a third authoritative record or lets an SMS recipient, invite-code holder, or proxy cross an unverified authority boundary.

## Alternatives and tensions

Requiring several match factors reduces takeover risk but can exclude patients with stale demographics or no policy, so staff-assisted linking needs documented evidence, bounded duration, and later review. Migrating reminders to push lowers SMS cost but must not silently override purpose-specific preferences or strand users after token expiry. Install attribution improves the funnel yet increases tracking risk; campaign identifiers should be opaque, short-lived, and separated from health content. One-device family access improves usability while making subject confusion dangerous, and location-assisted arrival may improve flow but must remain optional rather than a condition of service.

## Open questions

- Which proofing levels permit administrative task access, clinical content access through openChart, proxy activation, and patient-initiated financial actions?
- When should an active app become the preferred channel, and what failed-push or device-change evidence automatically restores SMS fallback without changing consent?
- Which funnel dimensions are useful enough for export while remaining safe from re-identification or inference about visit type?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Mobile Registration And Previsit Completion](openpractice-feature-list-mobile-registration-and-previsit.md), [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md), [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md).
