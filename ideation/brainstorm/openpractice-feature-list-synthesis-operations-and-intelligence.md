# Synthesis: Operations And Intelligence — Brainstorm

Status: brainstorm
Kind: architecture
Summary: work queues and metrics are one operating system in openPractice — every domain event can become accountable, SLA-bounded queue work, and every metric carries versioned open semantics — replacing the report-then-spreadsheet management loop and making practice performance reproducible, inspectable, and comparable.
Topics: openpractice-feature-list, operations-intelligence-synthesis, synthesis, operations, reporting
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Events become worked queues** — stale eligibility, expiring authorizations, missing charges, stalled claims, unpaid balances, and credential expiries arrive in role-routed inboxes with due dates, not in reports someone must remember to run.
- **Metrics as a published product** — fill rate, no-show rate, days in AR, first-pass yield, denial rate, net collection rate ship with formula versions, tests, owners, and citation rights.
- **Manager control surface** — one view answers "what is stuck, since when, whose is it, what has it cost" across locations without exporting to spreadsheets.
- **Provenance everywhere** — every queue item traces to its originating event; every aggregate drills to authorized records under the same governing formula.

## Members and their joints

Atomic members: [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md), [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md).

### Queues ↔ Metrics

SLA clocks make operational performance measurable by construction: acceptance times, completion times, and escalation rates are queue-native events feeding the KPI pack — no retrospective time-study required. Chronic understaffing becomes visible before it harms cash flow.

### Routing ↔ Trust

Capability-aware routing respects least privilege by design; assignment rules cannot hand sensitive financial work to roles lacking authority, and every routing decision records its reason — automation that explains itself.

## Emergent behavior

The pair converts management from archaeology (run report, export, pivot, hope) into observation with accountability. Unattended work escalates visibly; staffing load quantifies itself; benchmark packages carry denominator and metric-version metadata so peer comparison survives scrutiny. The deployment gains an honest pulse.

## Tensions to hold

A universal inbox becomes noise without governed sources and priorities. Evidence-backed completion adds front-line friction; templates and source acknowledgments must absorb most of it. Live semantic queries keep dashboards fresh but burden transactional systems; warehouse freshness invites disputes over which formula-version answered the question.

## Recombination opportunities

Denial queues recombine with [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md) payer scorecards into collector worklists; access metrics recombine from [Synthesis: Front Office And Access](openpractice-feature-list-synthesis-front-office-and-access.md) into capacity forecasting; evidence packs recombine with [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md) for audit-ready operations narratives.

## Open questions

- Which queue types carry hard SLAs at launch versus advisory clocks?
- Do historical dashboards recompute under latest formulas or freeze at period-close versions?
- Can customer-defined peer groups deliver benchmark value before any network exists?

## Relationships

Owned by [Overview](openpractice-feature-list-overview.md). Cross-cluster: [Synthesis: Revenue Cycle](openpractice-feature-list-synthesis-revenue-cycle.md), [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md).
