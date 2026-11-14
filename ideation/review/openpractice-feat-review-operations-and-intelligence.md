# Feat Review: Operations And Intelligence

Status: brainstorm
Kind: review
Summary: The operations-and-intelligence cluster has the right spine — events become visible work, numbers become defensible — but roughly forty percent of its machinery (routing engines, acceptance SLAs, escalation ladders, delegation records, team-sharing, registry surfaces) is a forty-person billing department's vocabulary imposed on a three-person office that actually has a memory problem, a day-boundary problem, and an "is this normal?" problem.
Topics: openpractice-feat-review, operations-intelligence
Captured: 2026-08-26

## How to read this

Sixteen feats across three documents, each reviewed against one persona: **the practice manager who is also the front-desk lead**, in a practice with two to four total staff and a physician-owner who has ten minutes a month for numbers. The test applied throughout is not "is this a good capability" but "would this person open this screen on a Tuesday, and would the practice be worse off without it."

Verdicts used: `essential-core` · `differentiator` · `valuable-enhancer` · `questionable` · `defer`.

---

# Part A — Operations Tasks And Workforce

## A1. Unified operations inbox

**Verdict: essential-core.** This is the product for this cluster. A small practice's administrative failures are almost never "we did the wrong thing" — they are "nobody knew that was sitting there." One place where eligibility gaps, expiring auths, unpaid balances, and schedule holes all land is the single feature that replaces the sticky notes and the manager's memory. The danger is equally clear: six source domains firing into one list produces a two-hundred-item wall by day three, and a wall is abandoned exactly as fast as an EHR inbox.

**Make it more useful.** Default the view to **Today** — due today plus overdue — with everything else behind *Later* and *Someday*. No source-type tabs, no folders, no queue picker in beta. Every item must carry (a) a one-line imperative verb, (b) a deep link that lands on the exact screen where the work is done, and (c) a dollar figure or a date consequence. An item you cannot act on *from the inbox* is a notification, not work. Enforce a volume budget per source: if a detector emits more than ~10 items a day for this practice size, it is a report, not a queue, and it should be re-tuned before launch rather than shipped noisy.

**Alternate solutions.**
- *(Simpler)* **Today's Work** — one flat ordered list, no queues, no assignment. Sorted by `money at risk × age`. Twenty rows visible, "show older" beneath. At three people, everyone seeing everything and claiming by opening is not a compromise; it is the correct model.
- **Work lives on the record, not in an inbox.** Do not create a task entity at all. Define twelve *bad states* (visit with no charge, claim silent 30 days, eligibility stale before an appointment) as saved queries. The "inbox" is the union of those queries. This eliminates an entire bug class — a task saying "call about eligibility" when eligibility was fixed yesterday cannot exist, because the list *is* the state.
- **The inbox as a 7am printout.** A single page emailed and printed, worked down with a pen. Retrograde-sounding; it is what small offices already do with the day sheet, and it beats a screen for adoption.

**Reframe.** Not "a task management system." **Nothing that costs money waits unseen, and nobody has to remember to check anything.** The design target is that an empty screen at 5pm is both achievable and meaningful.

**Gaps it leaves.** No path for work that arrives by phone — the largest single source in a small office — so staff will keep a parallel paper list and the inbox will be structurally incomplete from week one. No end-of-day "is it safe to go home" state. No distinction between calendar-anchored work ("do this today") and appointment-anchored work ("do this before Mrs. Alvarez arrives Thursday"), which are worked on completely different rhythms. Nothing about what the inbox looks like when its primary worker is on vacation.

---

## A2. Capability-aware routing

**Verdict: defer.** Routing by skill, payer familiarity, workload, and capacity is a solution to a problem three people do not have. There is nobody to route to. Every rule is a configuration decision someone must make, maintain, and debug, and its dominant failure mode — work routed to the person who is out today — is strictly worse than no routing at all. This becomes real somewhere north of fifteen administrative FTEs.

**Make it more useful.** If anything ships: a **default owner per work type**, set once during guided setup with sensible product defaults, and *unassigned* as a legal, visible, non-alarming state. Drop skill matching, payer familiarity, capacity balancing, fallback chains, and bounded-automation scope from beta entirely. Keep "reason for routing" only where routing was automatic; when a human assigned it, the reason is "Dana assigned it" and does not need a record.

**Alternate solutions.**
- *(Simpler)* **Two buckets: mine and ours.** Everything starts in *ours*. Opening an item soft-claims it; the claim auto-releases after a few idle hours. No rules engine exists. This is how a three-person team already works and it degrades gracefully when someone is out.
- **Route only what permission requires.** The single routing decision that must be correct is keeping refunds, adjustments, and write-offs away from staff without authority. Express that as *visibility and action permission*, not as assignment logic. Everything else is "whoever gets to it."
- **Coverage-by-schedule, not routing-by-skill.** Work goes to whoever is on the front-desk station or the billing station today, read from the staff schedule the practice already keeps. One input, no rules, no matrix.

**Reframe.** "Who should do this?" is the wrong question at three people. The right one is **"is there anything nobody has picked up?"** Route nothing; surface the unclaimed.

**Gaps it leaves.** No concept of routing *to a day* — the Tuesday/Thursday part-time biller means work created Monday is unworkable until Tuesday, and a person-shaped assignment model has no way to say so. Nothing for the physician-owner who will receive two items a year and will never log in to find them (their work must reach them by text or paper, not by assignment). Nothing for the outsourced biller, who at this practice size is more likely to exist than a second in-house biller.

---

## A3. Time-bounded accountability

**Verdict: valuable-enhancer** for due dates and recurrence; the SLA apparatus specifically is **questionable**. Due dates and recurring obligations are load-bearing. Acceptance windows, completion SLAs, escalation ladders, and manager views are contract-defense instruments for organizations that owe someone a service level. In a three-person office the escalation target is the person who wrote the escalation rule. Escalating to yourself is theater with an audit trail.

**Make it more useful.** Replace SLA configuration with a **built-in, non-configurable aging scale per work type**, shipped as product defaults: eligibility stale after one day; authorization measured against the appointment date; unresponded claim at 21/30/45 days by payer default; patient balance by statement cycle. Render age as color *and* as a plain sentence with its consequence — "11 days, and this payer usually pays by day 18" beats any percentage. Zero SLA configuration screens in beta.

**Alternate solutions.**
- *(Simpler)* **Aging colors, no clocks.** Three states: fresh / getting old / this is a problem now. No pause-resume semantics, no business-hours calendars, no acceptance-versus-completion split. Those distinctions exist to make an SLA report defensible; a small practice has no counterparty to defend it to.
- **Deadlines derived from facts, never from policy.** Auth work is due before the appointment. Claim work is due before the timely-filing limit. Statement work is due at the next cycle. Every due date computes from a real date already in the system, so nobody *sets* due dates and none of them are arbitrary. This is simultaneously simpler and more correct than any SLA policy, and it removes the largest configuration surface in the cluster.
- **Escalation replaced by surfacing.** An aged item promotes itself to the top of everyone's Today list and appears on the morning huddle page. In a small practice, visibility *is* escalation.

**Reframe.** Not "did we meet our service level" but **"is anything rotting, and how much money is inside it?"** Age matters only because of consequence — filing limits, auth expiry, patient patience — so show the consequence and let the clock stay internal.

**Gaps it leaves.** No first-class **snooze with a reason** for work that legitimately must wait ("payer said call back on the 12th"). Without it, staff close valid items to clear red, and every aging metric becomes a lie within a month. No holiday or weekend awareness for "due today." Recurrence has no concept of *skipped* versus *behind* — recurring checklists that silently pile up unrun are the single most common way this feature dies in the field.

---

## A4. Resilient coverage

**Verdict: valuable-enhancer** for the underlying problem; **questionable** as specified. Planned delegation, out-of-office schedules, substitute authority grants, and complete handoff histories are HR-grade machinery. But the real problem — Dana is out Thursday and nobody knows what she was in the middle of — is *more* acute at three people than at fifty, because there is no redundancy to absorb it. The need is genuine; the proposed apparatus is three sizes too large.

**Make it more useful.** Make coverage a **fact about a day**, not a workflow. One control: "I'm out, these dates." Consequences: everything of mine becomes visible in the shared pool for those days, my name stays attached, and my recurring obligations surface on the huddle page flagged "Dana's, and she's out." No delegation objects, no substitute-authority grants, no acceptance handshake, no handoff ceremony.

**Alternate solutions.**
- *(Simpler)* **Pool by default, so absence orphans nothing.** If work is unassigned by default (see A2), coverage is a non-feature. The only residue is tacit knowledge — solve it with a single prompted handoff note on items you were mid-way through when you flagged yourself out.
- **One absence model, four consumers.** The same "out" record should drive staff coverage, provider availability, appointment templates, and closure days. If openPractice ends up with separate staff-out-of-office and provider-unavailability concepts, that is a defect, not two features.
- **Return-from-absence briefing.** The underexplored half. Coverage handles the leaving; nothing handles the coming back. A "while you were out" summary — what got done, what got covered, what is still yours — is higher value and lower cost than the delegation machinery.

**Reframe.** Not workforce management. **When someone is out, the practice does not lose its memory.** Absence is the test of whether work is written down or living in a head.

**Gaps it leaves.** *Unplanned* absence — the 7:40am sick call — is the common case, and a "planned out-of-office schedule" does not serve it at all. Nothing for whole-practice closure (holiday, weather, provider illness cascading to cancellations) and what becomes of everything due that day. Nothing for the temp, locum, or per-diem who needs a bounded one-day view, which is the actual staffing pattern at this size.

---

## A5. Evidence-backed completion

**Verdict: questionable** at the proposed scope; a thin slice is essential. Structured outcomes plus attachments plus source acknowledgements plus supervisor review on every closure is precisely the friction that drives staff back to sticky notes. But *why did this close* genuinely matters for the money-losing subset — write-offs, adjustments, abandoned claims, balances sent out — and the auto-captured portion is free.

**Make it more useful.** Two hard-coded tiers, no configuration. **Tier 1** (roughly 95% of items): one tap to close, outcome auto-derived from the state change the system already observed ("eligibility re-verified — active"), no note required, no fields. **Tier 2** (money-losing or patient-affecting closures): a required reason from a short fixed list, plus an optional note. Supervisor review does not ship in beta — the physician-owner is not reviewing task closures, and pretending otherwise builds a queue that only accumulates.

**Alternate solutions.**
- *(Simpler)* **Evidence as byproduct, never as form.** Make closure a *consequence* of doing the real action — rerunning eligibility, posting the adjustment, sending the statement — rather than a separate step. The ideal end state is that items close themselves when their originating condition clears and staff never "complete" anything; they just work.
- **Reopen instead of review.** Replace prospective supervisor review with cheap retrospective reopen: anything closed can be reopened with a reason, and reopened closures collect in a small "we got this wrong" list reviewed monthly. Voluntary and retrospective beats mandatory and prospective at every practice size, and especially at three people.
- **One universal evidence primitive: paste-a-thing.** A single field accepting a screenshot, a payer-portal confirmation number, or two lines of text. Small practices live in payer portals; the artifact that saves you a year later is a confirmation number, not a structured outcome taxonomy.

**Reframe.** Not "prove the work was done" but **"a year from now, when the payer says we never called, can we show what we did in under a minute?"** That recasts evidence from compliance burden to self-defense, which staff will actually maintain.

**Gaps it leaves.** No accommodation for work performed *outside* the system — the phone call, the fax, the payer portal — which is most of it; the evidence model must assume the action happened elsewhere and the system is only recording the receipt. No outcome for "closed because it is not worth chasing," a legitimate and frequent small-balance disposition. No bulk close when one external event resolves forty items (payer reprocesses a batch), which is the dominant resolution pattern in small-practice AR.

---

## A6. Administrative readiness guardrails

**Verdict: essential-core** for the warning half; the blocking half is dangerous. A lapsed license or expired payer enrollment in a three-person practice is silent revenue destruction discovered sixty days later in a denial batch. Expiry monitoring is high value and near-zero friction. A hard guard that halts scheduling because a credentialing date was not updated will get the product blamed for a lost clinic day and switched off permanently.

**Make it more useful.** Ship **warn-only** in beta, with the warning placed at the point of consequence rather than as a global gate: at claim submission, "Dr. Reyes's Aetna enrollment expired 8/1 — this claim will likely deny." One product-supplied escalating cadence: 90 / 60 / 30 / 7 days. Attach to each expiry a *who renews this and what is the first step* field, because the real failure is not detection — it is that nobody knew the renewal takes forty-five days.

**Alternate solutions.**
- *(Simpler)* **A credential wall calendar.** One screen: every provider crossed with every payer, license, DEA, CLIA, and malpractice line, colored by days remaining. No rules, no events, no guards. Reviewed once a month. For this domain a legible grid may genuinely beat a queue.
- **Soft pre-flight instead of hard block.** The check runs at the moment of consequence and offers "do it anyway" with a recorded reason. All the protection, none of the operational hostage-taking, and the recorded overrides become the monthly review agenda.
- **Expiry as forward-dated work.** Create the renewal item at `expiry − lead time`, with the renewal steps in the body. Monitoring that does not become work is monitoring nobody acts on.

**Reframe.** Not "guardrails" but **"no revenue-blocking date passes without somebody having been told three times."** The enemy is silence, not the action.

**Gaps it leaves.** The credentialing state that hurts small practices most is absent: a **newly hired provider not yet enrolled**, where the correct behavior is *hold claims and release on approval*, not warn — and the held-claim backlog needs its own visible bucket with a timely-filing clock. Nothing covers practice-level expiries (business license, malpractice, CLIA, W-9, EFT enrollment, BAAs). No tie to document evidence: the renewed certificate PDF has to land somewhere and update the date, or the calendar drifts out of truth within a year.

---

# Part B — Reporting And Analytics

## B1. Practice KPI pack

**Verdict: essential-core**, with the wrong list for this audience. Seven canonical metrics with definitions and freshness is close to right, but "accountable owners" per metric is comic at three people — the owner of every metric is the practice manager. And the list conflates rhythms: days in AR and net collection rate are monthly instruments; fill rate and unconfirmed appointments are daily ones. Presenting them together produces a dashboard that is stale for half its contents at any moment.

**Make it more useful.** Split by **rhythm, not by category**. *Daily five*, on the screen they already open: today's schedule fill, unconfirmed appointments for tomorrow, yesterday's visits without charges, claims stuck with no payer response, money collected yesterday. *Monthly six*: days in AR, net collection rate, first-pass yield, denial rate, no-show rate, visits versus same month last year. Never render a number without a direction arrow and a normal band — a bare percentage is unreadable to a non-analyst. Always show denominators: "no-show 8% (4 of 51)" is trusted; "8%" is disputed.

**Alternate solutions.**
- *(Simpler)* **Five numbers, one line each, no dashboard.** Placed at the top of the Today screen; tapping one drills to the rows behind it. Nobody in a small practice navigates *to* Analytics. The numbers must sit on the road already walked.
- **Answers, not metrics.** Replace KPI tiles with computed plain sentences: "You are collecting two cents less on the dollar than last quarter, mostly from BCBS denials for missing authorization." A small practice does not need a metric registry; it needs to be told what changed and why. Substantially harder to build, and closer to what the customer is actually paying for.
- **Metrics as exceptions only.** Show nothing when everything is normal. The dashboard is empty on a good day. Radical, and proportionate — attention is the scarcest resource in a three-person office.

**Reframe.** Not "practice performance analytics." **The six numbers a physician-owner would ask about at dinner: always right, always current, each explainable in one sentence.**

**Gaps it leaves.** No **cash in the door** number, which is the figure owners care about most and is nowhere in the list. Nothing per-provider in a practice where the two providers' comparative performance is the most politically live topic there is. No same-period-last-year comparison, though seasonality dominates ambulatory volume. No handling of the partial month — mid-month days-in-AR is misleading and needs an explicit annotation rather than a confidently wrong tile.

---

## B2. Explainable drill-downs

**Verdict: essential-core** — arguably more important than the metrics themselves. Tap a number, see the rows: this is the single behavior that earns trust. Without it, every figure is disputed and the practice manager reverts to exports and Excel within a month. With it, "denial rate 12%" stops being a judgment and becomes twelve claims to work.

**Make it more useful.** Make the terminal state of every drill-down a **worked list, not a read-only table** — the twelve denied claims arrive with action buttons on each row. This is the actual joint between queues and metrics and it is the highest-leverage single decision in this cluster. Fix the drill hierarchy (practice → location → provider → payer) as a constant for beta; do not build a pivot engine for a single-location, two-provider customer.

**Alternate solutions.**
- *(Simpler)* **Every metric is a saved query; drill-down is just showing its rows.** The number is literally `count(rows)`. No aggregate store, no separate detail path, correctness by construction, and drill-down costs nothing to build. Entirely viable at small-practice volumes — tens of thousands of rows, not billions.
- **Two levels only: number → rows, then filter.** Skip the dimensional intermediate levels for a single-location practice and let the row list be grouped after arrival. Fewer clicks to the actionable object.
- **A "why did this change?" button.** Drill down the *delta* rather than the dimension tree: show exactly which records entered and left the numerator and denominator since last period. This is what a human actually wants the moment a number moves, and no incumbent offers it.

**Reframe.** Not "drill-down." **Every number is a doorway to the work behind it.** A metric you cannot act on from is decoration.

**Gaps it leaves.** No escape hatch for a number that is wrong because the *data* is wrong — "these three visits have no charges, which is why yesterday looks bad" — and that explanation is needed far more often than a dimensional breakdown. Least-privilege drill-down is largely moot at three cross-trained staff, but the one real boundary (owner-only compensation and profitability views) is not called out. No printed or exported drill-down for the accountant, who is the third-most-frequent consumer of these numbers.

---

## B3. Open metric registry

**Verdict: differentiator** — for the project, not for the beta practice. Published, versioned, testable formulas are a real strategic wedge against incumbents whose numbers cannot be reproduced or disputed. But the practice manager will never open a registry. The value they receive is that the numbers are right and stable; the value openPractice receives is the citation right and the marketing position.

**Make it more useful.** Treat it as an **engineering artifact with a one-sentence UI**. In-product: a "how is this calculated?" tooltip on every number, in plain English, with a link out. Out-of-product: formulas live in the repository as versioned definitions with conformance fixtures and a changelog, rendered by the docs site. Do **not** build a registry browser, a version picker, or a formula editor in beta.

**Alternate solutions.**
- *(Simpler)* **Metric definitions as code plus a docs page.** One file per metric — formula, inclusions, exclusions, golden fixture, changelog. Ship the entire citation value with a static site and zero product surface.
- **Version-pinned outputs instead of a registry.** Make the *artifact* citable: every export and every board one-pager footer carries "openPractice metric pack v1.3, computed 2026-08-26, definitions at <url>." The citation right is the deliverable; the registry is an implementation detail.
- **Publish the conformance suite as the position.** A public test suite any vendor's numbers can be run against turns an internal artifact into an industry stance, at roughly the cost of writing the fixtures you needed anyway.

**Reframe.** Not "an open metric registry." **A number from this system can be defended in a room where someone disputes it, without opening the database.**

**Gaps it leaves.** No restatement policy in practice: when a formula version changes, who tells the owner that last quarter's number moved, and does the board deck from March remain true? This needs an explicit "as published on" stamp and an archived as-published copy. Nothing about aliasing to MGMA or payer definitions, which is precisely what makes an external comparison legitimate rather than merely reproducible.

---

## B4. Role-scoped team views

**Verdict: defer.** Saved filters, shareable dashboards, team subscriptions, and row-level sharing authorization are collaboration infrastructure for organizations with multiple teams. This practice has one team and it is everybody. Every hour spent here in beta buys nothing, and the permission surface it creates is a liability in a HIPAA product.

**Make it more useful.** Collapse to **two shipped, non-editable views**: "Front desk today" and "Owner's month." Personal saved filters are an acceptable low-cost convenience. Sharing, team objects, and subscriptions are not. Keep the underlying permission model — who may see money, who may issue refunds — but locate it in platform, not in reporting.

**Alternate solutions.**
- *(Simpler)* **No saved views; filter state lives in the URL.** Sharing is pasting a link. Zero new objects, zero new permission surface, zero migration risk.
- **Role picks the home screen once, and only that.** At setup each user chooses front desk / billing / owner, which selects a landing page and nothing else. Because role compression means people switch hats hourly, pair it with a one-click "show me everything."
- **The physician-owner gets email, not a login.** The realistic owner "view" at this size is a monthly one-page PDF. Building them a dashboard they will open twice is the classic small-practice product mistake.

**Reframe.** Not "role-scoped team views." **Each person opens the app to the one screen that is their job today, and reaches everything else in one click.**

**Gaps it leaves.** No continuity of "what's mine" across screens, which is the only personalization that matters at this size. Nothing at all for the **outside biller or contracted RCM vendor** — extremely common at two to four staff — which is a genuine, high-value scoping requirement and is worth more than every internal team-sharing feature combined.

---

## B5. Scheduled export center

**Verdict: valuable-enhancer**, and much narrower than described. The real instances are one to three recurring files: the bookkeeper's monthly package, possibly a payer or board file, possibly a productivity extract. That is a short list of named deliverables, not a center, and de-identification modes and delivery audit are enterprise shapes.

**Make it more useful.** Ship **named, purpose-built exports** instead of an export builder: "Month-end accounting package," "Deposit and settlement reconciliation," "Provider productivity." Fixed columns, fixed period, one "email monthly to <address>" toggle each. The value is that the file is correct and identical every month — not that it is configurable.

**Alternate solutions.**
- *(Simpler)* **A download button and a calendar reminder.** For a practice that closes the month once, a human pressing a button on the third is more reliable than a scheduler nobody monitors. Scheduling introduces silent-non-delivery as a failure mode with essentially no compensating benefit at one export per month.
- **Push to the destination, not to a person.** The accounting bridge should write into the accounting system or a watched folder directly, so no human ever handles a CSV. That is the real end state, and it deletes "export" as a user-facing concept.
- **Every list view exports what you are looking at.** A universal affordance that covers most ad-hoc demand at near-zero cost and preempts the request for a report builder.

**Reframe.** Not "scheduled export center." **The bookkeeper gets the same correct file on the same day every month without anyone remembering.**

**Gaps it leaves.** Nothing surfaces a *failed* delivery to a human who will notice. No guardrail against the practice emailing a PHI-bearing CSV to their accountant's consumer mailbox, which they will absolutely do. No story for re-running last month's export after a late correction — the restatement problem again, arriving here as a file that no longer matches the one the accountant already booked.

---

## B6. Forward-looking operations views

**Verdict: defer** for the forecasting; one component inside it is essential. Payer-mix projections and revenue forecasts with confidence ranges are analyst tooling that a practice manager will look at once. But the near-term, fully deterministic question — *what money is scheduled to arrive in the next thirty days* — is the highest-demand figure for a small-practice owner and is not a prediction at all. It is arithmetic over submitted claims, AR aging, and observed payer lag.

**Make it more useful.** Cut prediction. Ship **expected cash, next thirty days**, decomposed into its three visible components: submitted claims × observed payer lag, patient balances by age × observed collection rate, scheduled visits × average expected reimbursement. Show the components, not a single number; label it a projection and expose the inputs. Separately ship the capacity view — next fourteen days' fill by provider — because it is the one forward view that is actionable *this week*.

**Alternate solutions.**
- *(Simpler)* **Two counts and a calendar.** Open slots in the next fourteen days, and dollars submitted awaiting payment by age bucket. No models, no ranges. A practice manager can act on both before lunch.
- **Backward-looking with a trend beats forward-looking with a model.** A thirteen-week rolling line for four numbers is more interpretable and more trusted than any forecast. Humans extrapolate trend lines well and distrust machine numbers they cannot reproduce.
- **Same week last year.** Seasonality is the dominant signal in ambulatory volume, and this comparison is free once a year of data exists.

**Reframe.** Not "forecasting." **Will there be enough patients next month and enough money next month — and if not, what can I do about it this week?**

**Gaps it leaves.** No expense side at all; the owner's cash question is two-sided and openPractice will only ever hold half of it, which must be stated rather than implied away. No loop from a forecast gap to an action — an empty week two weeks out is only useful if it triggers recall or waitlist outreach. No handling of small-N: a two-provider practice's weekly figures are mostly noise, and a system that reports them confidently teaches the owner to overreact.

---

# Part C — Synthesis: Operations And Intelligence

## C1. Events become worked queues

**Verdict: essential-core** as a principle, **questionable** as an architecture. The principle is exactly right and is the correct spine for the cluster: a domain event should become visible work, not a report someone must remember to run. The word *queues* — plural, role-routed — is where it goes wrong, because plurality implies partition, and partition is the specific thing a tiny team must avoid.

**Make it more useful.** Specify the **event-to-work contract**, not the queue taxonomy. Every emitting domain must declare, per work type: the trigger condition, the **clearing condition** (this is the crucial and under-discussed half — it is what lets work close itself), the due-date derivation, the action link, and the money-at-risk expression. Ship a fixed catalog of roughly twelve work types. No user-defined work types in beta.

**Alternate solutions.**
- *(Simpler)* **One list, twelve types, no queues.** Same events, single destination, ordered by consequence.
- **Queues as saved filters over records in bad states.** No task entity, therefore no staleness, no reconciliation job, no divergence between what the list says and what is true.
- **Event as an in-context nudge.** The event decorates the place you would naturally go — the appointment shows "eligibility stale," the claim shows "no payer response in 32 days" — so work is discovered in context rather than in an inbox. Pair with a small residual list for events attached to nothing you would open today.

**Reframe.** **Every problem the system can see becomes a thing a human can finish, and disappears the moment it is fixed.** The disappearing half is the hard half and gets a fraction of the attention the arriving half gets.

**Gaps it leaves.** No home for events with no owning object — a payer policy change, an office-wide issue, a clearinghouse outage. No deduplication or collapsing when one underlying problem fires six events (one patient, six stale-eligibility checks) — this is how the wall gets built. And nothing detects the event that *should* have fired and did not: a silently failed detector is invisible by definition and produces both an empty queue and a reassuring metric.

---

## C2. Metrics as a published product

**Verdict: differentiator**, substantially overlapping B3. The synthesis framing is sound; the risk is that "published product" implies product surface — registry UI, subscriptions, version pickers — that this practice will never touch and that will consume beta engineering.

**Make it more useful.** State the promise as a **compatibility contract**, not a feature: within a major version, identical inputs produce identical numbers, and any change ships with a migration note and a restated history. That is a release-engineering rule enforced by tests. Give the practice exactly one artifact in return: a monthly one-pager whose footer names the metric-pack version.

**Alternate solutions.**
- *(Simpler)* **Freeze six formulas as v1 and never change them during beta.** If the answer to "which version?" is always "one," no versioning machinery is needed at all.
- **Publish the fixtures rather than the prose.** Golden input/output examples are more testable, more citable, and far less arguable than natural-language formula descriptions.

**Reframe.** **A number from openPractice means the same thing in March and in November, and we can prove it.**

**Gaps it leaves.** No policy for late-arriving data changing a closed period — the recurring blind spot across this whole cluster. Nothing about metrics whose definition is genuinely contested in the industry (denial rate depends on whether partial denials count), where the honest move is to ship both and name them. No named owner for the case where a formula is discovered to be wrong *after* the practice made a decision on the old number.

---

## C3. Manager control surface

**Verdict: essential-core**, and it should be the **home screen**, not a manager-only view. "What is stuck, since when, whose is it, what has it cost" is exactly the right question set. The error is framing it as a management artifact separate from the working surface — at three people the manager *is* the worker, and two screens will become one screen the first week or the manager screen will be abandoned.

**Make it more useful.** Merge it with the operations inbox into one scrollable, printable page. Top: the daily five. Middle: today's work sorted by consequence. Bottom: what has aged. Add the two things a small practice checks every morning that the description omits: **today's schedule at a glance with risk flags**, and **yesterday's close status**.

**Alternate solutions.**
- *(Simpler)* **A morning huddle page.** One printable page designed to be read aloud in four minutes at 8:00am: today's schedule and its risks, who is in and out, what is overdue, yesterday's numbers. Optimizes for a ritual most small practices already have rather than for a dashboard habit they do not.
- **A 6:30am email.** Identical content, zero navigation, readable on a phone in a parking lot. By a wide margin the highest-adoption form of this feature.
- **Print-first design.** Many small practices print the day sheet. Making this page print well on one sheet is a real constraint that materially improves the digital version.

**Reframe.** Not "manager control surface." **The four minutes at 8am that decide whether today goes well.**

**Gaps it leaves.** No evening counterpart, and end-of-day close is the higher-risk ritual — drawer, card batch, unposted charges, undispositioned no-shows. Nothing for the weekly rhythm (Friday: next week's authorizations and unconfirmed appointments). Nothing for the monthly owner rhythm. The cluster describes a continuous flow and a small practice runs on cycles.

---

## C4. Provenance everywhere

**Verdict: differentiator**, with a beta discipline. Provenance is the project's most honest wedge, it is nearly free if built into the substrate from day one, and it is nearly impossible to retrofit — so build it. But it must remain **invisible**. The practice does not want a provenance viewer; they want the answer to "why does this say that" in one click, roughly twice a year, and they want it very badly on those two occasions.

**Make it more useful.** Constrain to two artifacts. User-facing: a **"how did this get here?"** panel on any work item, claim, balance, or number, rendering the chain as plain sentences in reverse chronological order — not a graph, not a table of event IDs. Machine-facing: a correlation ID that survives every hop, so support answers a question without a database session.

**Alternate solutions.**
- *(Simpler)* **One append-only event log plus one renderer.** Provenance becomes a *view*, not a subsystem, and no domain builds its own parallel chain structure.
- **Sell it as the undo story.** The highest practical value of a complete chain is reversing a mistake safely. "We can always tell you what happened and put it back" is the same infrastructure sold on a benefit staff feel weekly rather than one they feel annually — and it drives better design, because a chain built for undo must be complete in a way a chain built for audit need not be.

**Reframe.** Not "provenance." **Nothing in this system is unexplainable, and no correction is frightening.**

**Gaps it leaves.** No beta policy on PHI in logs, flagged as a tension across the packet but never resolved, and provenance is where it will bite. No retention or volume policy for a self-hosted single-practice deployment. And nothing about provenance **across the EHR boundary** — every chain will terminate at "openChart said so," and that seam is exactly where real disputes live (the visit that was never billed because the note was never signed).

---

# Cluster-Level Review

## Missing feats

**End-of-Day Close.** A shipped, non-configurable evening checklist that reconciles the day: every arrived visit has charges, every payment collected is posted and matches the terminal batch and the drawer, every no-show and cancellation is dispositioned, tomorrow's unconfirmed appointments and missing authorizations are cleared. Produces a signed "day closed" record, and an unclosed day appears on tomorrow's huddle. This is the single largest omission in the cluster — it describes continuous queues, but a practice runs on days, and the day boundary is where money and data quality are actually lost.

**Morning Huddle Page.** One printable page generated at 6:30am and emailed: today's schedule with per-appointment risk flags (new patient, no eligibility, missing auth, outstanding balance, chronic no-show), who is in and out, overdue work, yesterday's five numbers, and any unclosed day. Also the app's landing screen. It attaches the product to a ritual that already exists instead of asking for a new one.

**Practice Health One-Pager.** A monthly auto-generated PDF for the physician-owner, designed for ten minutes: cash in, charges, collections versus expected, AR aging, visits versus the same month last year, the three biggest problems with dollar amounts attached, and one recommended action each. Emailed on the third. Footer names the metric-pack version, which is where the open-semantics differentiator finally becomes visible to a customer.

**Anomaly Watch.** Deterministic threshold-and-trend alerts on a fixed signal set — charges down more than X% against the trailing four-week mean, denial rate rising, a payer that stopped remitting, a claim batch never acknowledged, an appointment type suddenly no-showing, a detector that has emitted nothing in a week. Rules, not models; every alert names the records behind it and links straight to the work. This is the "charges down 40% this week" capability the cluster gestures at and never builds.

**Peer Context Without a Network.** openPractice cannot benchmark at N=1, so ship two substitutes: published, sourced specialty reference bands for the core metrics (with citations and explicit denominator/attribution notes), and the practice's own trailing-twelve baseline as the primary comparison. This answers "is 9% no-show bad?" on day one and defuses the benchmark open question entirely.

**Snooze With a Reason.** First-class deferral — "payer said call back on the 12th," "patient said they would mail it." The item leaves the list and returns on the date with its reason attached and its original age preserved. Without this, staff close valid work to clear red and every aging signal in the product becomes untrustworthy within a month.

**Phone-Call Capture.** A five-second "add a thing" affordance available from any screen, optionally attached to a patient or claim, with a due date and nothing else required. The largest source of work in a small office is a phone call, and systems that accept only system-generated work are structurally incomplete on day one — which is how the parallel paper list survives.

**Payer Follow-Up by Batch.** Group stalled claims by payer so one twenty-minute phone call resolves twelve items, with bulk disposition and one shared evidence note. Per-item queues actively fight the dominant work pattern in small-practice AR and make the queue feel slower than a spreadsheet.

**Coverage Day and While-You-Were-Out.** The absence pair: one control to mark yourself out (pooling your work and flagging your recurring obligations), and a return briefing summarizing what was done, what was covered, and what is still yours. Replaces the entire delegation subsystem at a fraction of the cost.

**Data Trust Panel.** A small, always-reachable list of everything that could currently make the numbers wrong: unposted charges, unmatched deposits, claims in an unknown state, arrived visits with no charge, a connector that has not responded today. Shown next to the metrics. This is cheap, and it prevents the trust collapse that kills every small-practice dashboard.

**Recurring Practice Calendar.** A small, product-supplied set of recurring obligations most practices never write down — credential and contract review dates, timely-filing sweeps, quarterly reconciliations, annual policy and BAA reviews, fee-schedule updates. Shipped populated, so the practice inherits an operating rhythm instead of being asked to invent one.

## Cross-feat gaps

**The metric and the queue disagree, and nothing notices.** Denial rate reports 12% while the denial queue holds four items because twenty were closed without being resolved. Every metric with a corresponding queue should display both counts side by side and flag divergence — that single check catches most of the ways this cluster can quietly fail.

**Work closed is not the same as problem solved.** Closure evidence and metric movement are entirely unconnected in these documents. A queue that empties while days-in-AR climbs is the definitive failure mode of worked-queue systems, and there is no mechanism here that would detect it.

**No day boundary anywhere.** Queues are continuous; metrics are periodic; nothing owns "the day is complete." Charges posted late land in the wrong period and silently restate yesterday's numbers, and no artifact in the cluster would surface that.

**SLA-derived KPIs measure the configuration, not the practice.** The synthesis's proudest joint is that SLA clocks feed the KPI pack without a time study. But if thresholds are editable and advisory, "% within SLA" becomes a measure of how the thresholds were last set, and it is uninterpretable across any period in which someone adjusted them. Deriving deadlines from real-world facts (A3) is what makes this joint honest.

**"What has it cost" has no source.** The manager control surface promises cost, but money-at-risk is never specified as an attribute the emitting domain computes at item creation. Without it, nothing in the inbox can be ranked by consequence, and the whole sort-by-consequence design collapses back into sort-by-date.

**Detector health is unmonitored.** Metrics presume events exist; queues presume detectors fire. A silently broken eligibility integration produces an empty queue and a healthy-looking metric simultaneously — the two signals reinforce a false conclusion instead of contradicting it. A per-detector "last fired / expected rate" canary is a few hours of work and closes the most dangerous hole in the cluster.

**The clinical boundary has no waiting state.** Administrative work routinely blocks on a clinical action (note unsigned, therefore no charge). Boundary discipline correctly puts that task in openChart, but the *consequence* — an unbilled visit aging toward a filing limit — lives here with no representation. An explicit "waiting on clinical" state, visible and measured but not owned, is required or these items will simply vanish from both systems.

**The outsourced biller is nowhere.** At two to four staff, the billing function is frequently external. Half these queues then belong to an actor outside the tenant, and neither document addresses external-participant work, scoping, or evidence — while B4 spends its budget on internal team sharing that this practice will never use.

**Corrections cross every boundary and are governed nowhere.** Queues produce closed items, metrics produce closed periods, exports produce delivered files, and a late correction invalidates all three. No document defines what happens. This is the same gap surfacing in A5, B3, B5, and C2, which is a strong signal it deserves its own owner.

## Intuitiveness assessment

The cluster is written in the native vocabulary of an operations organization: worklists, capability matching, acceptance windows, escalation ladders, delegation records, substitute authority, workgroup subscriptions, semantic registries, benchmark packages. That vocabulary accurately describes a forty-person billing department. The beta customer has three people.

A three-person practice has **no routing problem** (there is nobody to route to), **no escalation problem** (escalating to whom?), **no team-sharing problem** (there is one team), and **no metric-governance problem** (one person interprets every number and is also the person who would have configured it). What it has instead is a **memory problem** (things live in one person's head), a **day-boundary problem** (money and data quality leak at 5pm), an **"is this normal?" problem** (no reference for any number), and a **single-point-of-failure problem** (the one person who knows is out Thursday). The proposed feature set addresses the first partially and the other three barely at all.

The clearest tell is that the cluster measures **service levels** — an internal-org construct that presumes a counterparty — rather than **consequences**, which is what a practice actually experiences. Reorienting from clocks to consequences deletes most of the configuration surface and most of the enterprise smell at once, because a consequence-derived deadline needs no policy: it comes from the appointment date, the filing limit, the auth expiry, the statement cycle.

Proportionate would look like: **one screen** (the huddle, which is also the inbox, which is also the manager view), **one list** ordered by money × age, **twelve product-defined work types** each with a self-clearing condition, **colors instead of clocks**, **no assignment** beyond a soft claim, **five daily numbers and six monthly ones** with frozen formulas and tap-to-rows, **one evening close ritual**, **one monthly owner PDF**, and **a rules-based anomaly watcher**. Everything else waits for a customer with fifteen staff to ask for it. The strategic differentiators — provenance and open metric semantics — belong in the substrate and the repository, surfaced to the practice as exactly one panel and one tooltip. They are how openPractice wins the argument; they are not how the practice gets through Tuesday.

## Top 3 reframings

**1. From work management to "nothing waits unseen and nobody has to remember."** Judge every design decision in this cluster by whether it reduces what a human must hold in their head. That single test kills routing rules, SLA configuration, delegation workflows, and team sharing, and it promotes self-clearing work, snooze-with-a-reason, the morning huddle, the while-you-were-out briefing, and phone-call capture. It also explains why the *clearing* condition matters more than the trigger condition, which is the inversion this cluster most needs.

**2. From continuous queues to a day with a beginning and an end.** A practice does not experience a flow; it experiences days. A morning huddle opens the day, an end-of-day close finishes it, and the queue is simply what remains in between. This one reframe supplies the cluster's largest missing ritual, gives every metric a clean and defensible period boundary, converts "did we do everything?" from an unanswerable question into a checklist with a signature, and makes late-posted charges a caught exception rather than a silent restatement.

**3. From open metric semantics to "always-correct answers with a doorway" — and from SLAs to deadlines that come from facts.** For the practice: a small set of never-changing numbers, each with a plain-English definition, a normal band, and a tap-through to the rows behind it. For the project: formulas and golden fixtures published as repository artifacts. The registry is engineering; the answer is product. The same move applies to time — replace configurable service levels with deadlines derived from real dates already in the system (appointment, filing limit, auth expiry, statement cycle), rendered as aging colors with their consequence named. Nobody configures anything, and no deadline is ever arbitrary.
