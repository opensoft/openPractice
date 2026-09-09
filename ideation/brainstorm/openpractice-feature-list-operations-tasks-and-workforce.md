# Operations Tasks And Workforce — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should replace email-and-sticky-note administration with accountable, role-routed work queues whose creation, handoffs, evidence, escalations, and closure remain auditable without absorbing clinical tasking from the connected EHR.
Topics: openpractice-feature-list, operations, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Unified operations inbox** — eligibility corrections, authorization follow-ups, unpaid balances, schedule gaps, credential renewals, and local checklists arrive in one staff-facing queue without importing clinical tasks.
- **Capability-aware routing** — rules assign work by skill, role, location, payer familiarity, workload, or bounded MedxFactory scope, with an explicit reason for every routing decision.
- **Time-bounded accountability** — due dates, recurrence, acceptance windows, SLA clocks, escalation ladders, and manager views make unattended work visible before it harms access or cash flow.
- **Resilient coverage** — planned delegation and out-of-office schedules transfer eligible work while preserving the original assignee, substitute authority, and complete handoff history.
- **Evidence-backed completion** — structured outcomes, notes, attachments, source-system acknowledgements, and supervisor review prove why an item closed rather than merely marking it done.
- **Administrative readiness guardrails** — credential, license, enrollment, facility, location, and holiday records can warn or block administrative scheduling and billing actions when required prerequisites expire.

## Focus

What administrative work system gives a front office and revenue team a trustworthy daily operating rhythm, measurable service levels, and safe automation while leaving clinical decisions and clinical task lists in openChart or another EHR?

## Current state: OpenEMR baseline

OpenEMR provides user/group access controls, facility and provider assignments, calendars with recurring events and holidays, messages, patient transactions, broad appointment and financial reports, and audit-log facilities. Those pieces can support local follow-up conventions, but administrative tasking is largely assembled from messaging, screens, reports, and optional modules: there is no first-party cross-domain operations inbox, skill/location routing engine, recurrence and coverage model, queue SLA timer, escalation ladder, or structured completion-evidence contract. Facility governance is configuration-oriented rather than lifecycle-oriented, and deployment hardening, log review, backups, and operational controls remain the self-managing organization's responsibility.

Sources: OpenEMR Reporting wiki; OpenEMR Access Controls Listing; OpenEMR Database Structure; OpenEMR Users Guide scheduling and holidays sections; OpenEMR Audit Control wiki.

## Enterprise gap candidates

- athenaOne exposes non-clinician department worklists alongside assigned tasks, permission-scoped multi-department views, task-assignment routing rules, schedule administration with audit history, and performance dashboards informed by its network; the reusable pattern is managed routing plus visible exceptions, not importing its clinical inbox.
- eClinicalWorks combines centralized multi-provider administration, front/back-office KPI views, automated work playlists using RPA, and revenue-cycle workflows; its breadth suggests that generated work and the queue receiving it must share one operating model.
- AdvancedMD emphasizes a unified day-to-day practice workflow, configurable financial dashboards, scheduled reporting, and provider/location visibility; it demonstrates the value of turning operational signals into a manager's concise control surface.
- NextGen supports due-dated tasks, user/workgroup assignment, reassignments, delegates, out-of-office schedules, and permission-limited access; these are strong coverage patterns even though much of its documented workflow is clinically anchored.
- Tebra/Kareo gives small practices role-shaped dashboard workflows, an automatically generated to-do list, high-priority outstanding items, and financial indicators; simplicity for a small front office is a constraint, not a lesser tier.

## Proposed feature set for openPractice

Parity floor: OpenEMR-equivalent users, roles, facilities, providers, calendars, holidays, messaging hooks, audit events, and administrative reports. Adopted gaps: one operational inbox fed by eligibility, authorization, patient-balance, scheduling, credentialing, and facility events; routing by skill/role/location with capacity and fallback rules; due dates, recurrence, SLA clocks, escalation ladders, delegation, and scheduled out-of-office coverage; front-desk opening/closing checklists; structured completion evidence and reopening reasons; credential, license, payer-enrollment, and expiry monitoring with configurable warnings or administrative ordering guards; multi-location holiday and closure administration; capability-scoped APIs through which MedxFactory may propose, claim, or complete only authorized work. Twist: every queue item has an immutable provenance chain from originating event and routing decisions through each handoff, evidence artifact, automation action, and final closure.

## Interfaces and boundaries

Consumes: administrative and financial events from scheduling, eligibility, authorizations, claims, remittance, patient balances, credentialing, and facility configuration; minimal identifiers and status signals from connected EHRs through FHIR R4 or governed adapters. Emits: queue state, SLA performance, staffing load, completion evidence, and escalation events to reporting, compliance review, and bounded automation. Owns administrative task lifecycle and workforce coverage; does not own encounters, orders, results, medications, clinical inboxes, or judgments about clinical readiness.

## Alternatives and tensions

A universal queue can become noisy unless sources, priorities, and ownership are governed. Automatic assignment improves throughput but can hide chronic understaffing or route sensitive work beyond authority. Hard expiry guards reduce billing and compliance risk yet can halt legitimate operations when source data is stale, so warning, override, evidence, and retrospective review policies need separation. Detailed completion evidence strengthens auditability but adds front-line friction; templates and source-system acknowledgements should minimize duplicate entry.

## Open questions

- Which queue types need acceptance SLAs, completion SLAs, both, or neither?
- Should routing optimize continuity, workload balance, specialized payer knowledge, or an explicitly weighted policy?
- Which credential and facility expiries may block scheduling or billing, and who may perform a time-limited override?

## Relationships

Clustered in [Synthesis: Operations And Intelligence](openpractice-feature-list-synthesis-operations-and-intelligence.md). Adjacent: [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md), [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md), [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md).
