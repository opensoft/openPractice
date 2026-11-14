# Synthesis: Front Office And Access — Brainstorm

Status: brainstorm
Kind: architecture
Summary: registration, scheduling, communications, the HealthLinc patient app, AI-assisted paperwork, and presence-aware arrival are one funnel in openPractice — an identity-safe record becomes a constraint-safe booking, which becomes a consent-governed conversation completed before arrival — replacing the fragmented intake-plus-module assembly practices run today against leaders whose self-service breadth hides unaccountable automation.
Topics: openpractice-feature-list, front-office-synthesis, synthesis, registration, scheduling, patient-engagement, healthlinc-app, ai-intake, arrival-presence
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **One funnel, three surfaces** — registration readiness, schedule constraints, and communication consent evaluate as one chain so a patient can move from web form to confirmed appointment to reminder reply without staff re-entry.
- **Readiness-gated self-service** — self-booking, kiosk check-in, and digital intake only offer steps the administrative record can safely support; gaps route to staff queues with reasons instead of failing silently.
- **Risk-shaped outreach** — no-show risk, channel preference, quiet hours, and do-not-contact status jointly choose reminder cadence; nothing is denied or penalized on prediction alone.
- **Vacancy recovery as workflow** — cancellations trigger ranked, expiring, fully logged waitlist offers across SMS/email/voice with fairness reporting.
- **One patient surface, two backends** — the HealthLinc app renders administrative tasks from openPractice and EHR-authored instruments from openChart, routing every datum to its owning versioned contract instead of becoming a third silo.
- **AI-assisted completion with receipts** — plain-language explanations, confirmed draft answers, and cited administrative Q&A speed paperwork while every exchange leaves a decision record.
- **Presence without friction** — GPS geofence and lobby Bluetooth signals mark arrivals with sensor provenance while manual, kiosk, and SMS-reply paths remain first-class for patients without devices.

## Members and their joints

Atomic members: [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md), [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md), [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md), [HealthLinc App Onboarding](openpractice-feature-list-healthlinc-app-onboarding.md), [Mobile Registration And Previsit Completion](openpractice-feature-list-mobile-registration-and-previsit.md), [AI Intake Assistance](openpractice-feature-list-ai-intake-assistance.md), [Arrival Presence Detection](openpractice-feature-list-arrival-presence-detection.md).

### Registration ↔ Scheduling

The versioned registration-readiness contract is what makes constrained self-scheduling safe: booking rules consume readiness signals so incomplete records get guided completion, not silent downstream denials. Vendors bolt these together; openPractice makes readiness the shared primitive.

### Registration ↔ Communications

Consent evidence, recipient roles (patient/guarantor/proxy/subscriber), and confidential-contact flags are captured once at intake and enforced everywhere messages flow — including VIP restrictions that suppress directory-style search in kiosk and campaign surfaces.

### Scheduling ↔ Communications

Reminder cadence engines read no-show risk and appointment type; waitlist cascades ride two-way threads; replies ("running late", "cancel") become schedule events with full attempt history. The communication decision record explains every send after the fact.

### Onboarding ↔ Registration

Appointment-aware SMS invitations carry expiring invite codes redeemed only after name/DOB/policy verification, producing one identity reference both backends accept — adoption funnel stages (sent→installed→linked→first-task-completed) become reporting exports, and the verified link is what lets paperwork start before arrival.

### Mobile paperwork ↔ AI assistance

The copilot operates inside HealthLinc forms, not beside them: explanations attach to questions, confirmed suggestions differentiate from patient-entered values in the submission record, and clinical-question refusals route to openChart channels or staff queues as operations work.

### Presence ↔ Flow

Geofence and beacon events feed flow boards, wait-time tracking, and room-ready push notifications with sensor provenance attached, while manual check-in parity keeps arrival measurable for every patient, device or not.

## Emergent behavior

The sextet turns access into a measured funnel: identity-safe intake raises clean-registration rates, constraint transparency raises fill rate and lowers third-next-available, risk-shaped cadences lower no-shows without punitive overbooking, app-delivered paperwork arrives before the visit, and presence signals make arrival honest about how it knows. Every stage emits first-class metrics, so "why did this patient no-show" becomes answerable from data rather than folklore.

## Tensions to hold

Self-service depth competes with equity — patients without smartphones or documents need assisted paths that never dead-end. A unified communications service is a high-availability dependency across the whole funnel. Predictive features face cold starts and bias; rules-first defaults with cohort monitoring are mandatory, not optional polish. Location and Bluetooth sensing must stay purpose-limited, revocable, and disclosed or trust dies at the front door; AI assistance must never drift from confirmed suggestions into silent autofill or clinical advice.

## Recombination opportunities

Coverage confidence scores recombine from [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md) to make financial clearance visible at booking; estimates recombine from [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md) into pre-arrival payment offers; funnel metrics recombine into [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md) as the access KPI pack.

## Open questions

- Which minimum registration-readiness level unlocks true unattended self-booking?
- Do waitlist offers default to strict order or policy-selectable fairness modes?
- Where do inbound replies land first: domain queues or one operational inbox?

## Relationships

Owned by [Overview](openpractice-feature-list-overview.md). Cross-cluster: [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md), [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md).
