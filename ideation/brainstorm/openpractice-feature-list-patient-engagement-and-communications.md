# Patient Engagement And Communications — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should provide a consent-governed omnichannel operations layer that turns reminders, intake requests, statements, payment prompts, and cohort outreach into accountable conversations while categorically leaving clinical results and EHR-owned content outside its authority.
Topics: openpractice-feature-list, patient-engagement, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Unified operational threads** — join inbound and outbound SMS, email, and voice attempts into one patient/guarantor conversation with delivery, reply, handoff, and disposition history.
- **Cadence orchestration** — schedule appointment reminders, intake nudges, statements, and payment notifications by event, urgency, channel preference, response, and local quiet hours.
- **Consent decision service** — evaluate recipient, purpose, channel, jurisdiction, do-not-contact status, and evidence of consent before every send, with no bypass hidden in campaign tooling.
- **Cohort campaigns with guardrails** — let authorized staff define operational audiences, preview exclusions and message volume, approve content, and halt or revoke queued sends.
- **Digital task delivery** — send secure links for registration or intake forms, appointment actions, statements, and patient-initiated payment flows without placing sensitive detail in the message body.
- **Communication accountability** — expose attempt logs, opt-outs, failures, replies awaiting staff, and campaign outcomes as auditable operational data rather than vendor-console residue.

## Focus

Which consent-governed communication capabilities reduce no-shows and accelerate patient payment while preserving channel choice, privacy, human financial authority, and a hard boundary around EHR-owned clinical messaging?

## Current state: OpenEMR baseline

OpenEMR includes appointment reminder and recall mechanisms, configurable email and SMS notifications, portal secure messaging, portal forms, and patient communication preferences; commercial modules such as MedEx expand automated reminders, two-way texting, recall, and mass messaging. Core deployments require gateway and scheduled-job configuration, while richer channel orchestration remains module-dependent. The baseline lacks one purpose-aware consent evaluator, an enforced cross-channel do-not-contact registry, coherent SMS/email/voice threads, campaign approval gates, and a unified attempt log spanning operational reminders, intake, statements, and payment notices.

Sources: OpenEMR Project Wiki, “Sms and Email Notification Howtos”; OpenEMR Project Wiki, “The OpenEMR 6.0+ Patient Portal”; athenahealth Help, “Service Overview — athenaCommunicator,” “User Guide — ReminderCall,” and “Self-Pay Reminder Messages”; NextGen Healthcare, “Personalize Communication for Patients and Practice Staff”; AdvancedMD, “Patient Engagement.”

## Enterprise gap candidates

- athenahealth combines automated phone, email, SMS, portal messaging, targeted campaigns, appointment actions, self-pay reminders, communication history, opt-out handling, and bounded sending windows; its services-led RCM model connects outreach closely to collections while preserving configured payment authorization paths.
- eClinicalWorks uses the healow ecosystem for reminders, secure links, previsit tasks, messaging, and payments at broad scale, including billing automation around the wider suite; its breadth and known usability friction make cross-channel continuity and staff work-queue clarity key competitive openings.
- AdvancedMD offers messaging, intake and consent forms, reminders, kiosk, portal, telehealth, and self-scheduling as integrated but modular engagement tools, aligning with clinics that need staged adoption.
- NextGen Patient Engage supports SMS, voice, email, chat, message libraries, broadcasts, intake forms, delivery rules, and configurable earliest/latest contact hours, fitting specialty and larger-group workflow configuration needs.
- Tebra/Kareo focuses on independent practices by combining patient communications, online scheduling, intake, reputation and acquisition workflows, statements, and payments; a Frappe-native alternative should preserve that simplicity without coupling outreach consent to marketing convenience.

## Proposed feature set for openPractice

Parity floor: appointment reminders and recalls, portal notices, configurable email/SMS delivery, patient preferences, templates, and basic logs. Adopted gaps: two-way SMS, email, and voice threads; event-driven reminder cadences; digital intake-form delivery; statement and payment notifications linking to human-authorized payment flows; cohort broadcasts with preview and approval; purpose- and channel-specific consent evidence; global and scoped do-not-contact enforcement; one communication-attempt ledger; quiet hours, holiday calendars, batching, retries, delivery receipts, reply queues, and vendor failover adapters. Twist: every proposed send has a machine-readable communication decision record showing purpose, recipient authority, consent evidence, exclusions considered, chosen channel, and approving actor, so automation can be powerful without becoming unaccountable.

## Interfaces and boundaries

Consumes appointments and recalls from scheduling, registration and representative relationships, communication preferences and consent evidence, intake task links, and billing-approved statement or payment-notification intents. Emits delivery events, replies, opt-outs, unresolved-contact tasks, and engagement metrics to the owning operational domains and reporting. Message bodies carry minimum necessary administrative content; secure links provide sensitive detail. Clinical results messaging, diagnosis discussions, medication or order communication, and all other clinical content belong to openChart or the connected EHR. MedxFactory may draft, target, or send only through bounded purpose/channel scopes and required approval gates; it cannot initiate charges, refunds, or payment commitments.

## Alternatives and tensions

A single communications service improves enforcement and history but creates a high-availability dependency across workflows. SMS reach competes with privacy and identity uncertainty on shared phones; generic notifications plus secure retrieval reduce exposure but add friction. Central do-not-contact rules need purpose-specific exceptions for legally required notices without becoming an operator loophole. Batch efficiency must not violate local time, accessibility, language, or urgent cancellation timing, and vendor failover must not duplicate sends.

## Open questions

- Which consent purposes and recipient roles form the minimum interoperable vocabulary across patient, guarantor, proxy, and subscriber communications?
- Should replies remain in an openPractice operational inbox, route directly to domain work queues, or use both with explicit ownership?
- Which notices are mandatory despite a marketing or convenience opt-out, and how should that legal basis be represented and reviewed?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md), [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md).
