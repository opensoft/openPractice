# Scheduling And Patient Access — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should make scheduling a revenue-protecting access funnel whose explicit constraints, automated vacancy recovery, risk-aware outreach, and measurable capacity outcomes remain safe for patient self-service and adaptable across independent clinics and multi-location groups.
Topics: openpractice-feature-list, scheduling, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Rules-as-data booking** — represent appointment type, provider, location, resource, duration, patient class, lead time, and prerequisite constraints in versioned, testable configurations.
- **Vacancy recovery cascade** — rank eligible waitlist entries after cancellation, issue expiring offers in controlled waves, and preserve fairness, consent, and a complete offer history.
- **Risk-shaped attendance strategy** — score no-show likelihood to choose reminder cadence or staff review without denying slots, covertly overbooking, or penalizing protected cohorts.
- **Constrained self-scheduling** — expose only a safe vocabulary of approved appointment types and slots while explaining when staff assistance is required.
- **Administrative pathway booking** — reserve linked steps, locations, and resources for admin-defined sequences without encoding clinical decisions or documentation.
- **Access observability** — publish fill rate, third-next-available, cancellation recovery, offer acceptance, and no-show patterns as first-class reporting events.

## Focus

Which scheduling capabilities convert the calendar from a passive record into a measurable, revenue-protecting access funnel without transferring clinical triage authority into openPractice?

## Current state: OpenEMR baseline

OpenEMR provides appointment calendars with categories and colors, recurring events, provider and facility views, open-slot search, appointment-type restrictions, reminders and recalls, portal appointment requests, and patient-flow tracker boards with check-in status. SMS reminders, two-way texting, mass outreach, kiosk or mobile check-in, and telehealth connections vary by module or external service. The baseline does not provide native predictive attendance intelligence, automatic waitlist offer cascades, a governed self-scheduling constraint vocabulary, or robust sequential booking across dependent resources.

Sources: OpenEMR Project Wiki, “Features,” “Patient Flow Board,” and “Sms and Email Notification Howtos”; NextGen Healthcare, “Patient Self-Scheduling” and “Appointment Search Ahead”; athenahealth FHIR Implementation Guide, “Patient Scheduling Configuration”; athenahealth, “What’s new in the athenaOne Summer Release.”

## Enterprise gap candidates

- athenahealth exposes appointment-type booking configurations and now promotes automated waitlist notification, self-service check-in, and integrated engagement; its network-scale administrative model shows how eligibility or encounter-status changes can route work without embedding every rule in code.
- eClinicalWorks couples broad scheduling and healow engagement with contactless confirmation, previsit completion, arrival signaling, and payments; its suite breadth is valuable, while reported usability friction argues for one explainable access workflow rather than many surfaces.
- AdvancedMD is strongest as a modular scheduling and practice-management option, with configurable calendars, self-scheduling, reminders, telehealth administration, and scheduling-oriented automation suited to practices that adopt capabilities incrementally.
- NextGen targets specialty and mid-to-large groups with cross-practice appointment search, event/location/resource constraints, rules-based text and web self-scheduling, actionable reminders, and add-on smart-waitlist behavior.
- Tebra/Kareo optimizes for independent practices by joining online booking, reminders, intake, telehealth access, billing, and patient-acquisition surfaces; the opportunity is comparable simplicity with deeper constraint transparency and exportable metrics.

## Proposed feature set for openPractice

Parity floor: multi-location provider and resource calendars, categories, recurring availability, appointment status, slot search, reminders, recalls, portal requests, and arrival tracking. Adopted gaps: a constraint engine with rules stored as versioned data; safe patient self-scheduling; cancellation-triggered waitlist offer cascades; no-show risk signals feeding consent-aware reminder strategies and human work queues; kiosk and contactless check-in; recall-driven booking; administrative telehealth slot types; and linked sequential pathway templates for admin-defined care sequences. Twist: fill rate, third-next-available, no-show by cohort, vacancy-recovery time, and constraint-rejection reasons are immutable first-class exports to the reporting domain, making access performance inspectable rather than trapped inside a calendar.

## Interfaces and boundaries

Consumes administrative patient readiness from registration, coverage and authorization signals, provider/location/resource configuration, communication consent, and a bounded appointment-type vocabulary synchronized from openChart or another EHR. Emits appointments through FHIR R4-compatible exchange where feasible, arrival and cancellation events, waitlist offers, reminder intents, pathway reservations, and access metrics. openPractice owns operational slots and access workflow; openChart owns clinical triage, encounter documentation, orders, results, and decisions about what care is clinically appropriate. MedxFactory may propose or execute only explicitly scoped booking actions within configured constraints.

## Alternatives and tensions

Highly expressive rules can reproduce opaque professional-services customization, while a narrow vocabulary may not fit specialties; versioned templates and simulation should precede custom extensions. No-show models face cold starts and bias, so rules-first operation, cohort monitoring, and non-punitive use are mandatory. Sequential holds protect pathways but can strand capacity when one step fails. Waitlist speed must be balanced against equitable ordering, channel consent, accessibility, and patients who cannot respond instantly.

## Open questions

- Which constraint primitives cover the first release without becoming a general-purpose rules language?
- Should waitlist offers be strictly ordered, batched among equally eligible patients, or policy-selectable with fairness reporting?
- What minimum sample size and bias checks are required before a practice can enable model-based no-show scoring?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md), [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md), [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md).
