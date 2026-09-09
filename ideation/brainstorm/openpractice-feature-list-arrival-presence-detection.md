# Arrival Presence Detection — Brainstorm

Status: brainstorm
Kind: report
Summary: HealthLinc should turn explicitly opted-in geofence and lobby-beacon observations into short-lived, provenance-bearing arrival evidence for openPractice while preserving manual access, minimizing location retention, and never confusing an administrative presence signal with a clinical fact.
Topics: openpractice-feature-list, arrival-presence, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Appointment-scoped geofence invitation** — HealthLinc offers arrival detection for one upcoming appointment with a location-specific radius, a plain-language purpose, explicit opt-in, and revocation before or during monitoring.
- **Battery-aware boundary sensing** — platform geofence and significant-location-change events wake precise checks near the clinic rather than keeping continuous high-accuracy GPS active.
- **Lobby BLE confirmation** — privacy-enhanced beacons provide an indoor proximity observation where GPS degrades, using rotating or anonymized identifiers and accepting presence only for an active appointment window.
- **Evidence-shaped arrival state** — staff see GPS, BLE, QR, kiosk, SMS, or manual provenance, observation age, and confidence instead of one opaque arrived flag.
- **Inclusive arrival ladder** — front-desk entry, kiosk tap, door QR, and SMS reply remain first-class alternatives so no appointment or service depends on owning a compatible smartphone.
- **Flow-aware patient updates** — accepted arrival events update the operations board and wait timer, while room-ready or delay notifications return to HealthLinc or the patient's configured non-app channel.
- **Policy-aware exception routing** — configurable late-arrival rules create human-owned prompts and work items, while no-show analytics can distinguish absent, declined sensing, unavailable signal, and confirmed-late cases.
- **Purpose-limited location ledger** — consent evidence and arrival provenance are retained under policy, raw coordinates and beacon observations expire quickly, and reporting receives aggregation rather than reusable movement histories.

## Focus

How can HealthLinc, the owner's working name for the ecosystem patient app, let a practice know that an expected patient is physically present without requiring a front-desk exchange, imposing a smartphone, enabling covert tracking, or overstating what imperfect sensors establish?

## Current state: OpenEMR baseline

OpenEMR provides configurable appointment statuses, check-in and check-out semantics, arrival timestamps, room assignments, timers, a patient flow board, and visit-flow reports. Its documented workflow has front-desk staff mark an appointment arrived, after which board status and elapsed-time measures update; installations may also configure arrival to create an encounter. The reviewed baseline does not describe appointment-scoped GPS geofences, patient-phone BLE lobby confirmation, consent and revocation evidence, sensor confidence, or privacy-limited location retention, and its manual arrival model does not distinguish how presence was established.

Sources: OpenEMR Patient Flow Board, FlowboardV7, Appointment Statuses, and Generic Medical Encounter Workflow wikis; Healthengine Check-Ins FAQ; Jayex Patient Check-In System and Medrics Check-In and Queue pages; Apple Core Location region-monitoring, significant-change, energy-efficiency, and iBeacon guidance; Android geofencing and location battery guidance; Bluetooth SIG Riverside Healthcare RTLS story and Kontakt.io Virginia Hospital Center story; Google Eddystone-EID specification and privacy research; HHS Minimum Necessary Requirement guidance.

## Enterprise gap candidates

- Ambulatory check-in products already use device geolocation to limit mobile arrival to a clinic perimeter and combine app, SMS, QR, kiosk, queue, and staff-notification paths; capability parity therefore means channel choice and operational integration, not background surveillance.
- Apple and Android expose system-managed region monitoring intended to avoid continuous polling, but accuracy, latency, permissions, operating-system limits, and battery tradeoffs make geofence entry evidence rather than proof of lobby presence.
- Healthcare deployments demonstrate BLE's usefulness for indoor wayfinding, real-time location, flow timing, and presence-adjacent workflows; an outpatient arrival design can use the infrastructure more narrowly by observing a lobby beacon through the patient's opted-in app rather than issuing persistent patient tags.
- Static iBeacon identifiers can be observed over the air, while Eddystone-EID demonstrates encrypted identifiers that rotate and resolve only for authorized services; privacy-enhanced deployment should prefer ephemeral identifiers, scoped resolvers, key rotation, and anti-replay controls over public stable IDs.
- Flow metrics become more trustworthy when arrival records retain source, timestamp, confidence, and corrections, allowing reports to separate sensor-detected, patient-asserted, and staff-verified events instead of treating every timestamp as equally certain.

## Proposed feature set for openPractice

Parity floor: configurable appointment statuses, manual arrival, kiosk and QR check-in, SMS confirmation, real-time flow boards, elapsed-wait tracking, room-ready messaging, late-arrival work queues, and no-show reporting. Adopted gaps: appointment-specific HealthLinc consent and revocation; configurable geofence radius and activation window; battery-aware region or significant-location-change triggers near the boundary instead of continuous tracking; BLE lobby confirmation with rotating or anonymized beacon identifiers, controlled resolution, replay resistance, and validity only beside an active nearby appointment; visible sensor provenance, observation age, and confidence; practice signage disclosing beacon-assisted presence; automatic board and wait-time updates; return notifications through the app or alternate channel; human-reviewed late-arrival policy hooks; analytics that preserve uncertainty; manual, kiosk, QR, and SMS fallbacks with equal service access; short raw-location retention, deletion, purpose limitation, aggregate-only metrics, and an explicit prohibition on sale or secondary use; plus consent evidence linked to communication and consent records. Twist: every arrival event is an evidence object carrying sensor provenance and confidence, so downstream wait-time, rooming, late, and no-show measures remain honest about how the system knows.

## Interfaces and boundaries

Consumes: appointment identity and location, configured radius and time window, explicit patient permission state, OS-delivered region events, privacy-enhanced lobby beacon observations, manual and self-service confirmations, and practice arrival policy. Emits: administrative arrival evidence, board transitions, wait-timer inputs, room-ready notifications, exception work items, consent records, and de-identified aggregate measures to openPractice. Owns administrative check-in and flow state; does not continuously track a person, infer presence outside the appointment window, deny access when sensing is declined, create clinical facts or encounters, expose location to openChart, or alter encounter documentation, which remains under openChart authority.

## Alternatives and tensions

Geofence-only detection needs no lobby hardware but can trigger from a nearby road or adjacent suite; BLE improves indoor specificity while adding deployment, battery, resolver, spoofing, and accessibility burdens. Automatic arrival reduces friction but false positives affect queue fairness, so a configurable confidence threshold may mark probable presence for staff review rather than finalize every arrival. A small radius improves specificity yet becomes brittle under ordinary Wi-Fi and GPS error, whereas a large radius risks premature check-in. Stable beacon identifiers simplify operations but enable unwanted observation; ephemeral identifiers and short-lived signed assertions improve privacy at greater key-management cost. Detailed coordinates could support investigations and analytics but violate purpose limitation when provenance category and coarse evidence are sufficient.

## Open questions

- Which combinations of geofence dwell, BLE observation, appointment window, and patient confirmation justify probable versus confirmed arrival at each location type?
- How short can raw coordinate and beacon-observation retention be while still supporting correction, security investigation, consent disputes, and reliable offline synchronization?
- Should a low-confidence event notify staff only, ask the patient to confirm, or update the flow board with a visibly provisional state?
- Which beacon protocol and resolver design provides portable on-premise operation, rotating identifiers, key lifecycle controls, and equivalent behavior across iOS and Android?

## Relationships

Clustered in [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md). Adjacent: [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md), [HealthLinc App Onboarding](openpractice-feature-list-healthlinc-app-onboarding.md), [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md), [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md).
