# Feat Review: Revenue Cycle

Status: brainstorm
Kind: review
Summary: all 28 revenue-cycle feats reviewed for a 2-4 person ambulatory practice — 15 essential-core, 4 differentiators, 6 enhancers, 2 questionable, 1 defer — with the cluster's real risk being not missing capability but a provenance chain built as an auditable API instead of a generator of appeal packets, secondary claims, and stuck-money worklists, plus twelve unowned seams (secondary/COB derivation, timely filing, paper remits, recoupments, credit-balance clocks) where a small practice's money actually falls through.
Topics: openpractice-feat-review, revenue-cycle
Captured: 2026-08-26

## How to read this

Feat IDs are assigned here for reference (`CA-*`, `CC-*`, `CL-*`, `PB-*`, `SY-*`) in source-document order; the brainstorm packets do not number them. Verdicts are graded against one specific reader: a small ambulatory practice with 2-4 staff, one biller (possibly outsourced, possibly the office manager on Tuesdays), no IT capacity, and no tolerance for configuration surfaces. "Essential-core" means the beta is not a product without it. "Differentiator" means it beats OpenEMR and holds its own against athenahealth/Tebra. "Valuable-enhancer" means real value, wrong sequence. "Questionable" means the feat as stated will produce the wrong artifact. "Defer" means the compliance or build cost exceeds beta value.

---

# Coverage And Authorizations

## CA-1 — Eligibility at every operational checkpoint

**Verdict: essential-core.** Coverage failure is the largest preventable denial class a small practice faces (terminated coverage, wrong member ID, unresolved COB, patient-not-found), and every instance costs 30-45 days of AR plus a rework touch. A practice with one biller cannot absorb a steady trickle of these; the checkpoint discipline is the difference between a front-desk conversation and a write-off. This is the origin event of the entire money chain, so getting it wrong poisons estimates, statements, and claims downstream.

**Make it more useful.** Delete the configurability. Ship exactly two runs — a nightly sweep of appointments T-3 through T+1, and an automatic re-check fired by check-in — and never ask a practice to design a batch policy. Suppress results that are identical to the prior check; a solo biller should see deltas and hard failures only. Collapse the schedule badge to three states (Clear / Check / Unknown) with a one-line reason, and put a retry ladder behind the "Unknown" state so transient payer timeouts never reach a human.

**Alternate/better solutions.**
1. *Simpler mechanism*: stop modeling eligibility as a transaction to run and model it as a property of the appointment — a `coverage_as_of` timestamp with a state. Nobody "runs eligibility"; the biller only sees appointments whose property went stale or red. This eliminates a queue, a job configuration screen, and a mental model.
2. *Upstream data quality removes most of the work*: a large share of 271 failures are not coverage failures at all — they are name/DOB/member-ID mismatches. Put insurance-card capture with payer-ID normalization at registration, and add a "did the 271 echo back the demographics we sent?" assertion. Fixing the registration record once eliminates the recurring transaction failure, converting an eligibility feature into a registration feature.
3. *Let the clearinghouse do the sweep*: most clearinghouses already offer scheduled batch eligibility with a normalized payload. Buy the sweep; own only the delta detection, the badge, and the worklist. That is one adapter instead of a scheduler, a retry engine, and a 271 parser.

**Reframe.** Not "verify eligibility before the visit" but: *keep every future appointment's coverage assertion fresh enough to bill, and interrupt a human only when it stops being true.* Eligibility is a cache-invalidation problem, not a transaction problem — and cache problems have well-understood solutions (TTL by payer class, invalidate on contradiction, re-derive dependents).

**Gaps it leaves.** Medicaid managed-care churn, where the 271 returns the state program but not the plan actually responsible. MSP questionnaire obligations for Medicare patients. Workers' comp and auto liability, which have no 270/271 at all and need an entirely different record (claim number, adjuster, date of injury, jurisdiction). Self-pay patients, who get no checkpoint at all even though the Good Faith Estimate clock starts at scheduling. Retroactive Medicaid eligibility granted after the date of service, which needs a re-check-the-past sweep, not a look-forward one. Capitated members, where active eligibility does not imply a billable event. And the distinction between payer silence, a timeout, and a genuine "inactive" — three states this feat currently collapses into failure.

**Provenance twist check.** Genuine, not gestural: the 271 is the first link of the chain and the only contemporaneous evidence of what the payer said. But "raw 271 retained" is auditor value, not biller value. Make it useful by making it an argument, not an archive: when a claim denies for terminated or non-covered coverage, one click surfaces the 271 that was active on the date of service in the payer's own words and drafts the appeal from it. Provenance earns its keep the moment it stops being a record and starts being a rebuttal.

## CA-2 — Benefit-detail workspace

**Verdict: valuable-enhancer.** A narrow-service-mix practice needs about six numbers, not a workspace, and payer-reported deductible accumulators are chronically stale because claims in flight are not reflected. Building a rich normalization surface invites false precision, which is worse than a candid range — a patient quoted $40 who owes $312 costs more trust and more collection effort than a patient quoted "$40-$300, we won't know until your plan processes it."

**Make it more useful.** Replace the workspace with a *benefit strip* rendered inline on the appointment and on the estimate: in-network yes/no, copay for this service type, deductible remaining, coinsurance percentage, as-of date, one caveat line. Scope the service-type codes to the five this practice actually uses rather than the full STC list. Normalize only the fields that feed arithmetic; render everything else as the payer's own human-readable text behind one link.

**Alternate/better solutions.**
1. *Simpler mechanism*: cache only what the estimator consumes and drop the normalization UI entirely. Zero screens, one strip, one link to the raw response.
2. *Use your own data instead of the payer's*: your posted remits plus your own charges for that patient this year give a practice-observed deductible position that is frequently fresher than the payer's echo. Show both, label the discrepancy, and let the front desk quote the conservative one. This is cheap, differentiating, and honest.
3. *Let the payer API do it*: payer FHIR Coverage/ExplanationOfBenefit and patient-access endpoints increasingly return accumulators directly. Design the strip as an interface with 271 as one adapter, so richer sources drop in without reworking the display.

**Reframe.** Not "display the benefits" but: *produce the three numbers the front desk will say out loud, with an honest error bar and a timestamp.* Everything the front desk will not say out loud is documentation, not product.

**Gaps it leaves.** Embedded and family-tier deductibles. Separate behavioral-health or therapy benefit maximums and visit caps. Habilitative/rehabilitative splits. HRA/HSA/FSA first-dollar arrangements that make the copay display wrong. Carve-out vendors for lab, imaging, and behavioral health where the responding payer is not the paying payer. Out-of-network benefits and the No Surprises Act's limits on balance billing. Telehealth-specific cost-share variance. Medicare Advantage versus traditional Medicare behavioral differences. And secondary coverage, which the primary's 271 rarely reveals but which changes every number on the strip.

**Provenance twist check.** Strong candidate, currently underexploited. Bind each estimate line to the exact benefit segment it was computed from, so a statement dispute is answered with "your plan told us this, on this date, in these words." That converts provenance from an audit artifact into a dispute-defense artifact — and statement disputes are a front-desk time cost a small practice feels weekly.

## CA-3 — Coverage-change watch

**Verdict: essential-core, but it should not be a separate feat.** The behavior is indispensable; shipping it as a distinct capability doubles the configuration surface and creates a second place to look. Folded into CA-1 it is simply what CA-1 is for. As written it also stops at "route to staff," which is the least valuable half — the valuable half is invalidating everything downstream that was built on the old belief.

**Make it more useful.** Ship staleness windows as non-configurable payer-class defaults (Medicaid short, commercial medium, Medicare long). Render the output as a before → after delta on the day's huddle view, never as a raw response. Most importantly, make the watch *cascade*: when coverage changes, automatically list the estimates, statements, and claim-ready charges that were built on the superseded coverage.

**Alternate/better solutions.**
1. *Simpler mechanism*: there is no watch — there is a fixed re-check cadence and a diff. The "watch" is the diff. This removes an entire subsystem.
2. *Event-driven rather than polling*: a coverage-related denial on one claim is the highest-precision signal you will ever get. Let it trigger a re-check of every future appointment for that patient and every appointment for the same subscriber family. One denial should immunize the rest of the schedule — that is worth more than a hundred speculative rechecks.

**Reframe.** *The moment a payer tells us anything that contradicts what we believe, re-derive every downstream assertion that depended on it.* Coverage change is an invalidation cascade, not an alert.

**Gaps it leaves.** Retroactive termination, where the payer pays and later recoups. Newborn coverage added retroactively to the mother's policy. Coverage that changes between submission and adjudication, invisible to any pre-service watch. Estimates already given and statements already mailed under the old belief — nobody re-issues those, and this feat does not propose to. Marketplace grace-period suspension, where claims are held rather than denied.

**Provenance twist check.** This is where provenance becomes load-bearing rather than decorative: invalidation is only computable if every downstream artifact records which coverage version it was derived from. Make it useful by emitting the cascade as work — "3 estimates and 1 statement rest on coverage that changed" — which is provenance doing labor rather than storing history.

## CA-4 — Coordination-of-benefits control

**Verdict: essential-core.** COB errors are pure rework: the claim is clinically and codically correct and still fails, and the failure arrives 30-45 days later when the context is cold. For a solo biller this is the most demoralizing denial class because nothing about the claim was wrong except an ordering fact somebody could have asked the patient at the front desk. The "never silently choose financial responsibility" discipline is correct and rare.

**Make it more useful.** The model needs three things only: an ordered payer list per patient per date range, a reason for the order, and a "payer disagrees" state. Compute the birthday rule and the Medicare Secondary Payer hierarchy as defaults with a plain-language "why," so the biller confirms rather than reasons. Make "the payer told us otherwise" a first-class transition fed automatically from remittance and eligibility responses, because that is how COB errors are actually discovered.

**Alternate/better solutions.**
1. *Simpler mechanism*: no COB entity at all. An ordered payer sequence on the coverage record with effective dates, plus a conflict flag; the entire "control" is a conflict worklist.
2. *Ask the patient once, well*: two previsit questions ("do you have any other coverage?" / "is anyone else's plan covering you?") plus the MSP questionnaire for Medicare patients catches the majority at zero transaction cost. Upstream data quality genuinely can make most of this feat unnecessary — the feat exists because registration didn't ask.
3. *Let the payer answer*: Medicare eligibility responses and many commercial 271s carry other-payer information. Parse other-coverage from the response you already paid for and auto-propose the order rather than building a discovery workflow.

**Reframe.** Not "manage coordination of benefits" but: *know which payer is legally first before the claim leaves — and when we don't know, ask the patient rather than guess.* The output is a decision with a reason, not a data structure.

**Gaps it leaves.** Tertiary payers entirely. MSP questionnaire compliance and retention. Workers' comp and auto, where the primary is not health insurance and needs a claim number, adjuster, and date of injury. Liability cases with letters of protection. VA and TRICARE ordering rules. Medicaid's payer-of-last-resort status and its billing prerequisites. Most consequentially: **secondary claim assembly**, which requires the primary's adjudication amounts on the outgoing claim — the hardest and most valuable part of COB, and this feat does not mention it at all.

**Provenance twist check.** The strongest natural fit in the cluster and simultaneously its largest miss. A secondary claim is *literally* a provenance query rendered as X12: it needs the primary's paid amount, adjustment codes, and patient responsibility as data. Make it useful by auto-populating the secondary claim from the posted remit so it is a one-click derivation rather than a retyping exercise. This is the highest-return provenance use case in the entire packet, and nothing in the packet currently claims it.

## CA-5 — Authorization lifecycle

**Verdict: differentiator, with a beta scoping risk.** Whether this matters at all is specialty-dependent — primary care may touch five a month, while PT, behavioral health, ortho, and imaging-heavy practices touch fifty a week. The tracking half (registry, units, expiry) is indispensable wherever auths exist; the submission half (278, portals, fax orchestration) is a large build that will consume the beta if allowed to. Split them or the beta slips.

**Make it more useful.** Beta ships an authorization *registry*: payer, member, code set, date range, units authorized, units consumed, reference number, attachments, status — with unit decrementing driven automatically from posted charges and expiry alerts driven from the schedule. Submission starts as governed manual work: assemble the packet, deep-link the portal, paste the reference number back. That is roughly ninety percent of the value at fifteen percent of the build, and it leaves a clean seam for API adapters as impacted-payer prior-authorization requirements phase in across 2026-2027.

**Alternate/better solutions.**
1. *Simpler mechanism*: model an authorization as a **credit ledger** — an approval is a balance of units that charges draw down, with an expiry date on the balance. "Alerts" become a near-zero-balance report. This is structurally identical to a payment plan; reuse the object and the code.
2. *Let the payer/standard do it*: target Da Vinci PAS and coverage-requirements discovery as the interface, and adapt whatever auth product the clearinghouse already sells for beta. Do not build a submission engine as a first-party asset.
3. *Avoid the authorization entirely where possible*: a per-practice "does this payer/code combination require auth?" table, seeded from observed authorization denials, is cheap, self-improving, and prevents more harm than a submission workflow resolves.

**Reframe.** Not "manage prior authorizations" but: *never render a service whose approval is missing, expired, or already spent — and make the approval a countable balance the schedule can see.* Authorization is inventory management, and inventory has a well-known UI.

**Gaps it leaves.** Retrospective and urgent authorizations obtained after emergent care. Authorizations tied to a specific facility, NPI, or place of service. The referring-versus-rendering provider distinction. Peer-to-peer review scheduling. Concurrent review for ongoing series. Drug and injectable authorizations requiring code plus NDC plus units. Authorizations orphaned when the patient's plan changes mid-series. Standing referrals, common in Medicaid managed care, which behave differently from authorizations. And the fundamental caveat that an approval is not a payment guarantee — a display the benefit strip needs too.

**Provenance twist check.** Gestural as written. Make it real by linking authorization → consuming appointments → charge lines → claim → remittance, so an authorization-related denial is answered instantly with the reference number, approved range, unit position, and approval letter. Then add the inverse: when an authorization is denied after care was delivered, provenance names exactly which charges are now at risk and totals them. A dollar figure is a product; an audit trail is a cost.

## CA-6 — Authorization-to-charge gate

**Verdict: essential-core, conditional on CA-5's registry existing.** This is the point where authorization data becomes money rather than paperwork. The override-with-rationale design is correct: hard stops in a three-person practice are routed around by simply not entering the charge, which converts a controlled exception into invisible leakage.

**Make it more useful.** Run the same check at three moments with three different tones: at booking it is informational ("this usually needs auth"), at check-in it is actionable ("auth expired three days ago"), at charge review it holds the charge — never the visit. Default the override reason list to five options, one of which must be "authorization exists outside this system." Never let a financial gate become a clinical access decision.

**Alternate/better solutions.**
1. *Simpler mechanism*: do not build a gate object. Make the authorization check a rule in the same edit engine that runs modifier and bundling checks, with an earlier trigger point. The synthesis already proposes one rules engine with multiple trigger points for modifiers — apply that to authorization and the gate disappears as a separate concept.
2. *Invert it — reserve instead of check*: when an appointment is booked, reserve units against the authorization and release them on cancellation. Then over-consumption is impossible by construction and charge review inherits a correct answer rather than re-deriving one. This directly answers the source document's own open question about unit reservation.

**Reframe.** *Make authorization a resource that scheduling spends*, so a service cannot be booked beyond what is approved and the gate becomes a consequence of the model rather than a checkpoint in a workflow.

**Gaps it leaves.** Services added during the visit that were never planned — the single most common real-world case, and the one a booking-time reservation model handles worst. Modifier-driven scope changes that split a service beyond approved scope. Approvals covering a code family versus an exact code. Units versus visits versus dollars as the approved quantity. Global-period services under a single approval. And the "we did it anyway because the patient needed it" path, which needs a write-off classification rather than an error state.

**Provenance twist check.** Real, provided the override is a first-class event with an outcome. Make it useful by reporting monthly: "authorization gates were overridden 14 times; 9 paid, 5 denied for $3,100." That turns provenance into policy tuning and lets the gate teach itself which of its own rules are wrong — a self-improving control is a differentiator, a logged one is not.

---

# Charge Capture And Coding

## CC-1 — Versioned fee administration

**Verdict: valuable-enhancer at beta scale, essential-core later.** A 2-4 person practice has one fee schedule and a handful of contracts; the proposed dimension matrix (entity × location × provider × payer class × contract) is enterprise modeling that will be experienced as setup burden and abandoned. But effective-dating itself is non-negotiable: annual code and conversion-factor updates plus mid-year contract changes break naive designs and produce retroactive repricing errors that are painful to unwind.

**Make it more useful.** Ship one charge schedule with effective dates, plus a per-payer *expected allowed amount* table — which is really contract data and belongs with payer-contract work. Seed the charge schedule from the Medicare fee schedule times a practice-chosen multiplier, so day-one setup is one number rather than four hundred rows. Replace approval workflow with a single publish action that shows a diff preview; a diff is the control, not a signature chain.

**Alternate/better solutions.**
1. *Simpler mechanism*: store the fee as a **formula**, not a table — Medicare rate for the code, locality, and year, times a multiplier, with a floor. One rule governs the whole chargemaster and updates itself annually; exceptions are overrides layered on top rather than rows maintained by hand.
2. *Derive expected allowables from your own remits instead of from contracts*: the median paid amount per payer, code, and year is frequently more accurate than a contract PDF someone transcribed. Ship contract-from-remittance as the default and let the practice paste real contract rates only where they disagree with observed reality. This makes underpayment detection possible on day 90 without any contract loading project.

**Reframe.** *Never bill less than the payer would have paid, and always know what we should have been paid.* Fee administration is a two-number problem — charged and expected — with a time axis, not a catalog governance problem.

**Gaps it leaves.** Facility versus non-facility site-of-service differentials. Multiple-procedure payment reduction. Contracts priced at a lagging base year. Drug and vaccine pricing with NDC-based units and acquisition-cost programs. Anesthesia base-plus-time units. Capitated services that should never be charged at all. Sliding-fee scales. Self-pay and prompt-pay discount schedules, and their interaction with Good Faith Estimate obligations. Workers' comp state fee schedules, which are a separate rule system entirely.

**Provenance twist check.** Solidly real — a claim must remember which fee version priced it, or "why was this $185?" is unanswerable. Make it useful by turning it into a recovery tool: when a contract change is retroactive, provenance produces the list of claims priced under the superseded version and totals the rebill opportunity. "Here are 62 claims worth $4,800 to rebill" is provenance a practice will pay for; "here is the pricing history" is not.

## CC-2 — Governed code catalog

**Verdict: essential-core, but as infrastructure rather than as a feature.** Nothing in the cluster works without valid codes, so it must exist; but nothing about "governed catalog with release provenance" is what a biller experiences. The dominant design constraint the source document does not name is licensing: procedural code sets are licensed intellectual property, which is a genuine legal question for a self-hosted open-source product and should be a first-order design input, not a footnote.

**Make it more useful.** The feature a small practice actually needs is a **favorites list**: the forty codes this practice bills, ordered by frequency, each carrying the diagnoses historically paired with it. Ship the full catalog as a dataset the practice imports under its own license, and make favorites the primary interface. Then ship the annual-update ritual as a guided task: "twelve of your favorite codes changed; three were deleted; here are the crosswalks."

**Alternate/better solutions.**
1. *Simpler mechanism*: ship no catalog. Accept any code string, validate structurally, and let the clearinghouse and payer validate semantically; build the practice's catalog from what it actually bills. This sidesteps the licensing question entirely and is honest that code validity truth lives at the payer.
2. *Split by license status*: diagnosis codes and supply/service Level II codes are publicly available from federal sources; only the procedural code set is licensed. Ship two-thirds of the catalog free and make the licensed third a bring-your-own overlay. This is both a legal risk reduction and an open-source distribution advantage.

**Reframe.** *Make it impossible to bill a code that did not exist on the date of service, and make the forty codes you use daily one keystroke away.* Catalog governance is a validity-window problem plus an autocomplete problem — nothing more at this scale.

**Gaps it leaves.** Two different annual calendars (diagnosis codes change in the fall, procedure codes in January) intersecting one date of service. Codes deleted mid-series while a recurring authorization still references them. NDC-to-code mapping and unit formatting for drug claims. Waived-test modifiers. Category III codes and their coverage uncertainty. State Medicaid local codes. And the licensing exposure itself, which is unaddressed.

**Provenance twist check.** Weak and mechanical as stated — "release provenance" is audit theater unless it resolves a dispute. Make it useful narrowly: when a claim is rejected for an invalid code, one query answers "was this code valid on that date of service?" — which settles whether the error is ours or theirs and determines whether the next action is a correction or an appeal. That single question justifies the whole mechanism; nothing else about catalog provenance does.

## CC-3 — Event-derived draft charges

**Verdict: differentiator, and the highest-leverage feat in this document.** Duplicate entry between the EHR and the practice-management system is the largest recurring daily time cost in a small practice, and the clinical-boundary discipline turns what would be a hack into an architectural strength. If the beta ships nothing else from this document, it should ship this and the missing-charge reconciliation that pairs with it.

**Make it more useful.** The rule set that matters is tiny: appointment type implies a default code, modifier, place of service, and rendering provider; and if the EHR sent a coded encounter, use it. Seed ten rules from the practice's appointment types during onboarding. Present the result as a single daily grid — "yesterday's 18 visits produced 18 drafts, 15 clean" — rather than as per-encounter objects to open one at a time. A keyboard-only confirm-and-next flow will outperform any amount of intelligence.

**Alternate/better solutions.**
1. *Simpler and probably better*: do not derive — **receive**. If openChart can emit a coded encounter or charge resource, charge capture becomes ingestion plus review, and the rules engine becomes the degraded mode for EHRs that cannot emit. Design the contract first; the rules engine is the fallback, not the product.
2. *Superbill-from-schedule*: for a narrow service mix the appointment type is the charge most of the time. A one-screen "confirm yesterday" grid with pre-filled codes beats a rules engine on adoption, build cost, and explainability.
3. *Push it upstream*: make the appointment-type catalog carry billing intent at configuration time. Then derivation is a join rather than an engine, and the practice maintains it in the place they already maintain scheduling.

**Reframe.** *The visit already happened and the EHR already knows what it was — charge capture should be a confirmation, not an authoring act.* Success is measured in seconds per visit, not in rule coverage.

**Gaps it leaves.** Everything billable without an appointment: telephone and portal evaluation, chronic-care and remote-monitoring monthly time-based codes, after-hours services. Global-period visits that must post at zero with the right modifier logic. **Incident-to billing** and the rendering-versus-billing provider distinction — a genuine rules problem this document never mentions and a compliance exposure if guessed. Split and shared visits. Telehealth place-of-service and modifier combinations with heavy payer variance. Injections and vaccines requiring paired administration and product lines with NDC and units. Nurse-only visits and their supervision conditions. Non-billable no-show fees that are self-pay only.

**Provenance twist check.** This is the packet's flagship claim ("everything between this visit and this claim") and it is the most credible one — but it is pointed the wrong way for a small practice. Run the chain **backwards**: "show me every completed visit with no charge, no claim, or no payment." Provenance traversed forward is an audit; traversed backward it is a leakage detector, and leakage is money the practice can feel. Sold as auditability it is a cost center; sold as "here is $6,200 you never billed" it is a purchase.

## CC-4 — Missing-charge worklists

**Verdict: essential-core.** This is the most defensible revenue feature in the packet for a small practice: charge lag and charge leakage are invisible until month-end, run to low single-digit percentages of revenue, and are cheap to detect relative to their value. It also directly attacks the failure mode that role compression creates — nobody notices a missing charge because nobody owns "noticing."

**Make it more useful.** Reduce it to one dashboard number ("4 visits, 3 days old, unbilled") with a drill-in. Give every exclusion a reason *and an expiry*, so "intentionally not billed" cannot become a silent permanent leak. Age it into bands so normal lag doesn't generate noise. Critically, widen the scope beyond charges: include "charge posted but no claim created" and "claim created but never submitted" in the same list, because those are the same failure with a different location.

**Alternate/better solutions.**
1. *Simpler mechanism*: a daily reconciliation summary — "yesterday: 18 arrived, 17 charged, 1 open" — costs almost nothing and builds the habit that makes the worklist useful later.
2. *Make it a ritual, not a queue*: require a daily "close the day" action that cannot complete while an arrived appointment lacks a billing disposition. A tiny forced daily ritual beats a backlog nobody visits, and it fits role-compressed practices where queue ownership is undefined. This is the single best intuitiveness intervention in the cluster.

**Reframe.** *A day is not finished until every arrived patient has a billing disposition.* Leakage detection becomes a close, not a backlog — and a close has a natural owner, a natural time, and a natural completion signal.

**Gaps it leaves.** Non-appointment billables, as in CC-3. Charges rendered by a provider not yet effective with that payer, which should be held rather than billed and which requires enrollment state this cluster does not consume. Services legitimately without a charge under a global period or capitation. Externally billed reads and interpretations. Visits that should have been self-pay. Charges entered against the wrong date of service or provider, which are not missing but are wrong — a category this feat cannot see.

**Provenance twist check.** Excellent fit and under-exploited. Reconciliation should be continuous and bidirectional across the whole chain — appointment → charge → claim → remit → payment → deposit — with a single "stuck here, for this long, worth this much" state per unit of work. Under that model missing charges are one instance of a general money-didn't-move detector, which is both simpler to build and far more valuable than four separate worklists.

## CC-5 — Defensible coding assistance

**Verdict: defer for beta; valuable-enhancer later.** The small-practice beta packet already defers generated AI, and the economics support that: coding in a single small practice is repetitive and low-variance, so the marginal gain over a favorites list plus an evaluation-and-management helper is small, while the exposure — upcoding scrutiny, code-set licensing, protected information reaching a model, discoverability of suggestions in an audit — is large and asymmetric. The citation and review discipline described is correct; it is the model that should wait.

**Make it more useful.** Replace it with deterministic support that needs no model: a medical-decision-making-based level calculator fed from whatever structured elements the EHR sends, plus distribution anomaly nudging ("this appointment type bills at a level 3 in 82% of your history; you selected level 5"). Explainable, defensible, and it addresses the actual compliance risk — outlier curves — rather than the imagined one.

**Alternate/better solutions.**
1. *Simpler mechanism*: bill what the EHR signed. If the clinician coded it, the practice-management system validates rather than re-suggests; suggestion is only needed for uncoded documentation, which is a narrower and clearer problem.
2. *Retrospective instead of prospective*: run a monthly coding-pattern review — level curve versus specialty benchmark, modifier usage rates, denial rate by code — rather than per-encounter suggestions. Same compliance benefit, no per-claim risk, and one report a month is the right cadence for a solo biller.

**Reframe.** *Do not suggest codes; make it hard to submit a code that contradicts evidence we already have.* Moving from generation to contradiction-detection removes most of the liability and keeps most of the benefit.

**Gaps it leaves.** Who is legally the coder when a non-credentialed biller accepts a suggestion. Time-based versus decision-based level selection. Documentation arriving after the claim went out. Retention obligations for model version, input, and output. And the discoverability problem: suggestion records are evidence in an audit or a false-claims matter, which cuts both ways and is never discussed in the source.

**Provenance twist check.** The source treats provenance here as compliance armor, which is correct but not useful. Make it useful by (a) making citations clickable into the source document in openChart rather than copied, and (b) supporting the question "show me every claim whose code originated as a suggestion, and how they adjudicated." If the product cannot answer whether assistance improved outcomes, it should not ship assistance.

## CC-6 — Modifier and bundling checks

**Verdict: essential-core.** Procedure-to-procedure conflicts and missing modifiers are the highest-volume avoidable rejection class, the edit tables are published free and quarterly by the federal payer, and the fix is usually a single character. High value, low build, no vendor dependency — the best value-per-effort ratio in the cluster after missing-charge reconciliation.

**Make it more useful.** Ship the public edit tables refreshed quarterly, plus a small set of practice-specific rules derived from actual rejections. Write explanations as *fixes*, not violations: "add modifier 25 to the office visit" with a one-click apply, never an indicator-code explanation. Two severities only — fix before send, and send with a note.

**Alternate/better solutions.**
1. *Simpler mechanism*: let the clearinghouse scrub. Every major clearinghouse runs the federal edits plus proprietary payer rules and returns results pre-submission. Consuming and rendering those well is a fraction of the cost of maintaining a rules engine, and their payer-rule breadth will exceed yours for years. Own the explanation and the one-click fix; rent the rules.
2. *Learn from your own rejection history*: after ninety days, the practice's own rejection corpus is the highest-precision rule source it will ever have. "The last four times you billed this pair without a modifier, this payer rejected it" is more persuasive and more accurate than a generic edit, and it is free.

**Reframe.** *A claim should never leave the building carrying a rejection we have already seen.* Scrubbing is institutional memory, not a rulebook — which also means its success metric is recurrence rate, not rule count.

**Gaps it leaves.** Commercial payers' proprietary editors, which deviate from the federal edits in both directions. Documentation-support requirements that no edit can verify. Global surgical package modifier logic. Bilateral versus left/right payer preference. Anesthesia modifiers. Telehealth modifier drift across payers and years. Drug unit and NDC mismatches. And the reflexive-modifier risk: an engine that suggests a modifier every time an edit fires trains the practice into an audit exposure.

**Provenance twist check.** Real but shallow as stated. Make it useful by linking every override to its eventual adjudication so the rule set scores itself — "this rule fired 40 times; overrides paid 38 of 40, so the rule is wrong." A self-scoring scrubber is a genuine differentiator against static payer-rule networks, and it is the kind of thing an open-source community can pool.

---

# Claims And Remittance

## CL-1 — Pre-submission claim control

**Verdict: essential-core.** First-pass acceptance determines whether a small practice's cash flow works; each rejection costs a rework touch plus two to four weeks of aging. The versioned-rules-with-preserved-overrides design is right. The risk is building a rules *authoring* product when what is needed is a rules *outcome* product.

**Make it more useful.** Unify with CC-6 into one engine with three trigger points — charge review, pre-submission, and post-rejection learning — exactly as the synthesis proposes for modifiers. Every hold must name the field and offer an inline edit; a biller should never leave the hold screen to fix a hold. Two severities. And measure the engine by claims saved, not rules held.

**Alternate/better solutions.**
1. *Simpler mechanism*: submit and learn. Rely on clearinghouse pre-checks plus front-door rejections, and invest instead in same-day rejection turnaround. At low volume, cycle time dominates first-pass rate, and a rejected claim costs time rather than money — provided the rejection is seen, which is CL-2's job.
2. *Rules as data, not code*: express edits as a declarative table the practice's own biller can extend, so "this payer wants a referring provider on this code" is a row rather than a release. This is also what makes shared community rule packs possible later.

**Reframe.** *Never send a claim that will come back for a reason we already know* — with the corollary that the scrubber's job is to accumulate the practice's rejection memory, not to encode the industry's rulebook.

**Gaps it leaves.** Institutional claims (probably out of beta scope, but the packet should say so explicitly). **Corrected claims and voids** with the original payer control number — a first-class need this feat does not address. Secondary claim assembly with prior-payer adjudication amounts. Claims requiring attachments and the finalized attachment standards. Workers' comp and auto claims needing jurisdiction-specific forms and claim numbers. Taxonomy, NPI, and enrollment mismatches, which are invisible until rejection and are the most confusing rejections a small practice sees. Claims already past timely filing, which should be classified rather than sent.

**Provenance twist check.** Strong. Make it useful by attaching outcomes to overrides and surfacing them at the point of decision: "the last five times this rule was overridden, four paid." Without outcome feedback a rules engine ossifies into superstition; with it, the rule set becomes an accumulating, exportable asset — and an anonymized shared rule pack across openPractice deployments is a differentiator no proprietary vendor can copy without giving away its moat.

## CL-2 — Acknowledgments as work

**Verdict: essential-core.** The most common way a small practice silently loses money is a claim rejected at the clearinghouse or payer front door that nobody ever opened a portal to see. This is a pure make-the-invisible-visible feature, it is cheap, and it addresses the exact failure mode role compression produces.

**Make it more useful.** Collapse the transaction vocabulary entirely. Three user-visible states: accepted by payer, needs fix, waiting. Batch-level failures are systemic and should surface as one incident, not as N claim tasks. Per-claim front-door rejections are the real queue. Then add the rule that matters most: **any claim with no acknowledgment within 72 hours becomes work regardless of what any file says** — silence is the most dangerous state and the one no acknowledgment type describes.

**Alternate/better solutions.**
1. *Simpler mechanism*: a single "claims that haven't moved" list keyed on the age of the last state change, ignoring acknowledgment taxonomy entirely. Age-based monitoring catches every failure mode, including the ones you did not model.
2. *Let the clearinghouse normalize*: most vendors expose a status API that already merges front-door and payer-level responses. Consume that as the primary path and keep raw parsing as the portable fallback — fewer parsers, faster beta, and the connector-neutral state machine is preserved because the states are yours.

**Reframe.** *Every claim must be able to answer "where are you and who is holding you" at all times; any claim that cannot is by definition an exception.* Acknowledgment processing is a liveness property, not a file-parsing task.

**Gaps it leaves.** Attachment acknowledgment, which has its own lifecycle. Clearinghouse-level rejections that never produce a payer-level response. Payer rejections delivered on paper. Batch failures that silently drop a subset rather than the whole file. Duplicate submissions caused by resending an unacknowledged claim, which manufacture their own denial class. And the mapping from clearinghouse claim identifier to the payer's own claim control number — which is what every phone call, appeal, and corrected claim actually needs.

**Provenance twist check.** A natural fit; this is the chain's middle. Make it useful in the least glamorous possible way: **capture the payer's claim control number at acknowledgment and carry it everywhere.** That one identifier is worth more to a working biller than the entire provenance graph, because it is the key that makes every subsequent interaction with the payer possible.

## CL-3 — Continuous claim status

**Verdict: valuable-enhancer.** Status responses are famously low-information, and at small-practice volumes the remittance often arrives before an inquiry teaches you anything. Worth building — after acknowledgments and denials, not before. Polling everything on a schedule spends transactions to produce "pending."

**Make it more useful.** Poll selectively: only claims that exceed the payer's *observed* median payment time, computed from your own remittance history. Two polls per claim maximum. Present the answer as a change to the aging position, not as a status blob. The valuable output is not the status — it is "this claim is now abnormal for this payer."

**Alternate/better solutions.**
1. *Simpler mechanism*: an aging report with payer-specific expected payment dates learned from history. "This payer normally pays in 18 days; this claim is at 31" is more actionable than most status responses and costs zero transactions. This alternative is strictly better for beta.
2. *Payer APIs*: several payers now expose claim-status endpoints richer than the standard transaction. Model status as an interface with the standard transaction as the lowest-common-denominator adapter.

**Reframe.** *Do not ask where the claim is; know when it is late.* Status is a scheduling problem against a learned distribution, not a query problem — which is why your own remittance history is a better instrument than the payer's status endpoint.

**Gaps it leaves.** Claims the payer has no record of, which is the worst case and needs a resubmit-versus-duplicate decision the feat does not model. Status for secondary and tertiary claims. Claims paid to a different tax ID or provider. Claims paid by paper check with no electronic remittance. And the interaction with timely filing: by the time "not on file" is discovered, the remaining filing window may be short, which should escalate rather than queue.

**Provenance twist check.** Weak as stated. Make it useful by folding status into a single per-claim timeline that reads like a story — submitted, accepted, in process, paid — with elapsed days between hops. Provenance rendered as narrative is legible to a human worker; provenance rendered as a graph is not.

## CL-4 — Remittance exception operations

**Verdict: essential-core.** Manual remittance posting is the largest recurring time cost after charge entry, and auto-posting with a well-drawn exception queue converts directly into biller hours. The reversibility and balancing emphasis in the source is correct and is what distinguishes a safe implementation from a dangerous one.

**Make it more useful.** Define "clean" narrowly and auto-post aggressively inside it: matched by claim control number, paid at or above expected, no reversal, patient responsibility fully explained. Everything else queues — and the queue must be sorted by **dollars descending**, not by date, because a solo biller should always be working the largest recoverable amount. Reconcile the deposit before posting the details, because a posting error is recoverable and a reconciliation gap is not.

**Alternate/better solutions.**
1. *Simpler mechanism*: post everything automatically, make reversal trivial, and review a daily "what posted" digest. Reversibility is cheaper to build than judgment, and at one-practice volume the blast radius is small enough to prefer it.
2. *Variance-first design*: reframe posting as resolving the difference between expected and actual. If expected allowed amounts come from CC-1's learned or contracted rates, then every remittance either confirms the expectation or creates work — which unifies underpayment detection with posting instead of bolting it on as a separate later capability. This is the single most important structural suggestion in this document.

**Reframe.** *Posting is not data entry; it is closing the difference between what we expected and what happened.* Under that frame, "exception operations" and "underpayment recovery" stop being two features.

**Gaps it leaves.** Paper remittances and paper checks, still a meaningful share of small-practice cash, with no path here to produce the same objects. **Recoupments and offsets against future payments** — the hardest part of electronic remittance and the most confusing thing a small-practice biller encounters — entirely unmentioned. Interest payments. Capitation payments. Crossover indicators showing the payer already forwarded to the secondary, without which you generate duplicate secondary claims. Patient responsibility split into deductible, coinsurance, copay, and non-covered, which drives statements differently. Zero-pay remittances. Remittances spanning multiple tax IDs. Unapplied cash, credit balances, and refunds owed back to the payer.

**Provenance twist check.** The crown-jewel opportunity of the packet. Link each remittance line back to the estimate that quoted it and forward to the statement line it creates, and two high-value views exist for free: "we estimated $180, the plan applied $312 to deductible, here is why" for dispute defense, and "these lines paid $2,140 below expectation" for recovery. Provenance that generates a collection list is provenance that pays for itself in one month.

## CL-5 — Denial resolution workbench

**Verdict: differentiator with an essential kernel.** Denials are precisely where OpenEMR is weakest, so this is credible differentiation. The risk is the word "workbench": a solo biller will not visit a specialized destination. The kernel that must ship is smaller — every denial becomes one work item with one recommended next action, one deadline, and one dollar figure.

**Make it more useful.** Reduce the taxonomy to five operational buckets, each with a default action: coverage/COB → re-verify and rebill; coding/bundling → correct and resubmit; authorization/medical necessity → appeal with evidence; patient responsibility → move to statement; not our money → write off with a reason. Everything routes to one of five. Pre-fill the appeal from the chain. Display days remaining and dollars at risk on every row. Keep the original codes underneath but never show them as the primary label.

**Alternate/better solutions.**
1. *Simpler mechanism*: a denials list sorted by dollars with three buttons — rebill, appeal, write off — and a notes field for nuance. Ownership routing is enterprise machinery; in a small practice the biller is the owner and routing is overhead.
2. *Prevention over resolution*: every worked denial proposes a scrubber rule so the same denial cannot recur, and the workbench is measured by **recurrence rate** rather than throughput. Over six months this shrinks the workbench, which is the correct definition of success and the opposite of how denial products are usually sold.
3. *Template-plus-evidence appeals*: most first-level appeals are administrative rather than clinical. A letter template plus the claim control number plus the remittance image resolves a large share without touching the chart at all, which also avoids the clinical-boundary release question in the common case.

**Reframe.** *Prevent the denial classes you can predict; for the rest, make the next action obvious and the deadline visible* — plus the durable half: *every denial worked once must immunize the practice against its repeat.*

**Gaps it leaves.** Appeal levels beyond the first, each with its own forms and clocks. Appeal timely-filing distinct from claim timely-filing. The corrected-claim-versus-appeal decision, where choosing wrong burns the remaining window. Denials requiring clinical records and the release-governance path back to openChart. Denials on secondary claims. **Post-payment recoupments and audits** — denials that arrive after the money moved and behave nothing like pre-payment denials. Write-off approval thresholds. And patient-caused denials, which need to route into a patient conversation rather than a biller queue.

**Provenance twist check.** This is the packet's headline provenance use case and it is justified — but only if it is *generative*. One click must produce an appeal packet containing the eligibility response, the authorization, the charge, the claim as submitted, the acknowledgment, the remittance, and a letter. That converts an hour of assembly into a click, which is the single most concrete argument for building the chain at all. If the chain ships as an API without this artifact, the differentiator is unrealized.

## CL-6 — Payer accountability

**Verdict: valuable-enhancer at beta; differentiator at community scale.** One small practice has too little volume for statistically meaningful scorecards and usually cannot renegotiate contracts anyway — its leverage is near zero. The real value appears when many openPractice deployments contribute, which is a network effect no proprietary vendor can match without exposing its own book. Build the metric semantics now; build the dashboard later.

**Make it more useful.** For the single-practice beta, reduce to three numbers per payer — median days to pay, percent paid below expectation, percent first-pass — sorted by dollars at stake, with a "what changed this month" trend line. Trend detection matters more than levels at low volume. Define the metrics precisely enough now that they federate later without re-deriving history.

**Alternate/better solutions.**
1. *Simpler mechanism*: a monthly one-page payer report card, delivered rather than browsed. No dashboard, no drilldown, no interpretation burden.
2. *Make it collective*: an opt-in, anonymized cross-deployment benchmark — "your denial rate with this payer is 2.3× the network median" — is the actual differentiator here. It is a distribution and community feature wearing an analytics costume, and it should be named as its own feat rather than hidden inside a scorecard.

**Reframe.** *Answer the only two payer questions a small practice can act on: should we keep this contract, and what should we fix on our side first?* Everything else on a payer scorecard is decoration.

**Gaps it leaves.** Attribution between practice error and payer behavior — without it, scorecards mislead and can drive a practice to fire a payer over its own registration defects. Case-mix effects. Small-sample significance. Contract context, where a slow payer at a high rate beats a fast one at a low rate. And the underpayment metric's dependence on expected-rate data that may not exist, which makes the most persuasive number the least reliable one.

**Provenance twist check.** Real if every cell is drillable to the constituent claims and exportable as a payer-meeting packet. Also required: every "underpaid" figure must carry the source of its expected rate — loaded contract versus learned median — so the practice never argues from a number it cannot defend in the room.

---

# Patient Billing And Payments

## PB-1 — One understandable balance

**Verdict: essential-core.** Encounter-by-encounter statements are the leading cause of patient billing calls, and in a three-person practice those calls consume the front desk more than they consume the biller. Unification is high-value and mostly a presentation problem, which makes it unusually cheap for its impact.

**Make it more useful.** One statement per guarantor per cycle. The top of the page answers three questions — how much, why, what to do — and the detail sits below. Each line should carry the payer outcome that produced it ("plan paid $85, applied $60 to your deductible"), derived from the remittance, so the statement pre-empts the call rather than provoking it. Include a pay link that requires no account.

**Alternate/better solutions.**
1. *Simpler mechanism*: present the account as a running ledger with a balance, exactly like a credit-card statement. Everyone already owns that mental model; bespoke healthcare sectioning is a comprehension tax.
2. *Skip the statement*: below a threshold, a text message with a pay link and one explanatory line beats paper on both cost and collection rate. Make paper the fallback rather than the default, with an explicit equity carve-out so patients without devices are not disadvantaged.

**Reframe.** *A patient should never have to call to understand a bill.* Measure this feat by inbound billing-call volume, not by statement features — that single metric will drive better design decisions than any requirements list.

**Gaps it leaves.** Guarantor versus patient versus minor responsibility, including divorced-parent arrangements. Multiple legal entities on one statement. Returned mail and bad addresses. Statements issued during an active appeal, which should be suppressed. Statements resting on coverage that has since changed. Deceased patients and estates. Itemized-bill requests, which carry legal weight in several states. Language access. And credit balances, which display as negative numbers and confuse everyone — "we owe you $40" is the correct rendering.

**Provenance twist check.** This is where the estimate-to-payment twist becomes consumer-visible, and it is the best version of it. A statement line that says "we estimated $180 on March 3; your plan applied $312 to your deductible; here is the difference and why" prevents the call, defends the balance, and demonstrates the differentiator to the person who is paying. Provenance as explanation surface beats provenance as audit trail by a wide margin at this point in the chain.

## PB-2 — Estimate before obligation

**Verdict: essential-core, with a compliance floor.** Even setting aside competitive positioning, uninsured and self-pay patients are entitled to a Good Faith Estimate, which the small-practice beta packet already flags — so estimates are not an optional differentiator, they are a legal obligation the beta must meet. The versioning, ranges, expiry, and variance explanation are the right shape.

**Make it more useful.** Split one engine into two products and do not blur them: an *insured pre-service estimate*, which is a range used in a front-desk conversation, and a *self-pay Good Faith Estimate*, which is a document with required elements, delivery timing, and a retained copy. Seed insured estimates from the practice's own historical patient responsibility for that payer-and-service pair, which is the fastest route to accuracy without a contract-loading project.

**Alternate/better solutions.**
1. *Simpler mechanism*: an empirical estimate — "typical responsibility for this visit with your plan: $40-$95, based on 23 similar visits" — needs no contract data, no benefit parsing, and is often more honest than a computed figure. It also degrades gracefully when benefits are stale.
2. *Let the payer compute it*: advanced explanation-of-benefit obligations put this computation on payers. Design the estimate to consume a payer-produced estimate where available and fall back to local computation, so the product improves as payer APIs mature rather than competing with them.

**Reframe.** *Tell the patient what they will owe with an honest error bar before they consent — then keep the promise.* The promise-keeping half (variance tracking) is what builds trust and is the part incumbents skip.

**Gaps it leaves.** Co-provider and co-facility elements of a Good Faith Estimate. The patient-provider dispute-resolution process and the evidence retention it requires. Estimate expiry after a coverage change. Estimates for recurring series. Services that change during the visit. Scheduling-window timing rules for estimate delivery. Workers' comp and auto patients, who owe nothing and must not receive one. Medicaid patients with no cost share, likewise. And the Medicare advance-notice instrument, which is a distinct document with distinct rules and is unmentioned.

**Provenance twist check.** The strongest and most consumer-visible use in the packet. Variance reporting per payer and service tells the practice where its estimates are wrong, which is the only mechanism by which estimate accuracy improves; and the estimate-to-payment link answers "but you told me $80" instantly and credibly. Here provenance is a trust instrument, and trust is what converts an estimate into a collected dollar at check-in.

## PB-3 — Patient-directed payment choices

**Verdict: essential-core as rails; over-scoped as "choices."** The practice needs to take money at the desk, on a link, and in a reminder. Card plus one wallet covers the overwhelming majority of small-practice patient payments; bank transfer matters mainly for large post-insurance balances and payment plans. The absence of any PCI scope discussion in the source is the notable omission for a self-hosted open-source product.

**Make it more useful.** One processor adapter, card and wallet, with partial payment always available without staff permission. Treat payment plans as a first-class object shared with PB-4 and PB-6 rather than as a payment method. The design decision that matters is not the method list but the *moment*: check-in, statement link, reminder — three moments, one payment object.

**Alternate/better solutions.**
1. *Simpler mechanism*: use the processor's hosted payment page plus webhook posting. Own the ledger; rent the interface and, critically, the PCI scope. For a beta this removes an enormous compliance surface at negligible product cost.
2. *One adaptive action*: replace "choices" with a single Pay action that adapts to balance size and history — full, partial, or plan — so neither the patient nor the staff member makes a decision they are not equipped to make.

**Reframe.** *Make paying the path of least resistance at the three moments the patient is already thinking about the bill.* Method breadth is a vendor checkbox; moment coverage is the revenue lever.

**Gaps it leaves.** PCI scope and self-assessment level for a self-hosted deployment. Convenience-fee and surcharge legality, which varies by state. Refunds to expired or closed cards. Chargebacks and their ledger effect. Health-account card eligibility edge cases. Cash and check handling with drawer reconciliation. Payments posted to the wrong account or date of service. Family payments split across patients. Collections-agency remittances. Failed bank transfers.

**Provenance twist check.** Mostly plumbing here, with one discipline that matters enormously: every payment must carry its **allocation intent at capture time** — which charge, which estimate, which plan installment — rather than being allocated later. Deferred allocation is how unapplied cash accumulates and how reconciliation dies. Provenance at this link is an allocation contract, not a history.

## PB-4 — Consent-bound saved methods and autopay

**Verdict: valuable-enhancer, and a genuine trust differentiator.** Card on file materially raises collection on post-insurance balances, which is the hardest money in the practice to collect. The consent rigor described is correct, matches the ratified no-autonomous-money-movement constraint, and is a real marketing asset in a market where patients are justifiably suspicious of stored cards.

**Make it more useful.** One consent artifact with four fields — maximum per charge, maximum per period, expiry, and how notice is given — captured once at check-in with a signature and a plain-English receipt. Always notify before charging. Make revocation one tap from any receipt or statement, with no staff involvement. Do not build "authorization history" as a feature; it is simply the consent object's version list.

**Alternate/better solutions.**
1. *Simpler mechanism*: no autopay at all. "Save my card for whatever I owe after insurance, up to $X" is a single well-understood promise covering the dominant use case, with no scheduling machinery, no cadence design, and dramatically less consent-scope modeling.
2. *Pull instead of push*: send a pay link with one-tap confirmation against the saved token. Slightly lower conversion, far lower dispute and trust risk, and it sidesteps the entire recorded-authorization engine while preserving the outcome.

**Reframe.** *Get permission once, in words the patient would repeat back correctly, and then never surprise them.* Consent design is a comprehension problem; the data model is downstream of the sentence on the form.

**Gaps it leaves.** Re-consent when the amount pattern changes materially. State-level recurring-payment and e-signature disclosure rules. Bank-transfer authorization requirements, which differ from card rules. Card expiry and updater services. Retry policy on failed charges. Autopay during an active dispute or appeal — the source pauses *dunning* on dispute but never pauses *autopay*, which is the more damaging omission. Minors and guarantors. Deceased patients. And verbal revocation at the front desk, which must be honorable without a portal.

**Provenance twist check.** Genuine and, unusually, consumer-protective. Make it useful by rendering provenance *to the patient*: every automatic charge receipt cites the consent version and shows the remaining cap. That converts an audit feature into a trust feature, which is exactly the packet's stated differentiation strategy applied where a patient can actually see it.

## PB-5 — Controlled corrections

**Verdict: essential-core.** Credit balances and unrefunded overpayments are a real compliance exposure with a statutory clock for federal-program money and state unclaimed-property implications otherwise, and they are the most common source of ledger drift in small practices. The immutability and reason-code discipline is right; the approval model is calibrated for an organization that has two people available to approve.

**Make it more useful.** In a 2-4 person practice the maker and the checker are frequently the same person, so design for *visibility* rather than segregation: every correction lands in a daily corrections log the owner reviews, with dual approval required only above a dollar threshold. Then ship the thing that actually matters — a standing credit-balance worklist with an age, because the age is the compliance clock and nothing else in the packet is watching it.

**Alternate/better solutions.**
1. *Simpler mechanism*: make everything an immutable ledger entry with a reason and forbid deletion; "corrections" becomes a reporting view rather than a workflow. Immutability plus reversal is simpler, safer, and easier to reconcile than an approval engine.
2. *Prevent the credit rather than process it*: allocate payments to charges at capture and refuse over-allocation; most credit balances originate in copays collected on visits that adjudicate to zero liability, or in posting before adjudication. A "do not collect a copay when expected liability is zero" rule prevents more refunds than any refund workflow will ever process.

**Reframe.** *Money that isn't ours should leave on a clock.* Corrections are an obligation with a deadline, not an administrative capability — which changes the primary UI from a form to a countdown.

**Gaps it leaves.** Refunds owed to payers versus patients, which follow different rules and different clocks. The federal overpayment return deadline. Unclaimed-property escheatment for undeliverable refunds. Payer-applied offsets that create phantom credits. Transfers between family accounts. Small-balance write-off thresholds. Bad debt versus charity classification and its reporting consequences. Reversal of an entire posted remittance batch. And corrections after a period close, which must not silently reopen a closed period.

**Provenance twist check.** Essential and well-suited — but aim it at root cause, not at record. Trace every refund to the overpayment that caused it and to the estimate or copay rule that misquoted it, and "why do we keep refunding this payer's patients?" becomes answerable in one query, with an upstream fix as the answer. Provenance as root-cause analysis prevents the next twenty refunds; provenance as audit trail documents them.

## PB-6 — Gentle receivables orchestration

**Verdict: valuable-enhancer with an essential kernel.** The kernel — never dun someone whose claim is still in appeal, whose payment posted yesterday, or who is on a plan — prevents the most reputation-damaging failure a practice can commit, and it is cheap. The full preference-aware cadence engine is second-order and will generate configuration nobody maintains.

**Make it more useful.** Ship three cadences, not a designer: standard, plan, and paused. Auto-pause on open appeal, unposted payer activity, dispute flag, hardship application, or a coverage change affecting that date of service. Render the point-of-service prompt as one line on the check-in screen with an amount and a collect-or-defer action, never as a modal that trains staff to dismiss it.

**Alternate/better solutions.**
1. *Simpler mechanism*: collect at the desk. Time-of-service collection beats every dunning strategy on cost and yield; investing in estimate quality and the check-in prompt makes statements a thin backstop rather than a program.
2. *Reduce the debt instead of chasing it*: an automatic prompt-pay discount for payment within a short window frequently nets more than three statement cycles cost to produce. One rule replaces a cadence engine — with a caveat that discounts for patients in federal programs need care around inducement rules.

**Reframe.** *Collect at the moment the patient already trusts the number, and never chase money whose amount we are not yet sure of.* Dunning quality is almost entirely downstream of estimate quality and posting timeliness — which means the fix for collections usually lives in PB-2 and CL-4.

**Gaps it leaves.** Messaging-consent law for text and automated calls, which the source treats as a preference rather than a legal constraint and which carries per-message statutory exposure. Medical-debt credit-reporting changes. Financial-assistance screening obligations for practices taking certain funding. State medical-debt collection restrictions that increasingly reach providers directly. Language access. Patients in active treatment where collection pressure damages the therapeutic relationship. And balances created by retroactive coverage termination, which are legally the patient's but feel arbitrary and generate disputes at a high rate.

**Provenance twist check.** Real if reproducibility is the goal. Every statement must be reconstructable exactly as asserted on its date, because statement disputes are arguments about past assertions. And every pause must name its cause, so nobody restarts a cadence on a claim that is still under appeal. This is provenance as operational safety, and it is worth more than the audit story.

---

# Synthesis Feats

## SY-1 — Provenance-linked money chain

**Verdict: differentiator — genuinely unmatched in the open-source market — with one large execution risk.** Nobody ships an open, queryable eligibility-to-refund chain, and it is the packet's most defensible claim. The risk is that it ships as an API nobody in a three-person practice will ever call, which would make it a research asset rather than a product feature. Its beta value depends entirely on whether it surfaces as *work* rather than as *query*.

**Make it more useful.** Implement it as one durable object — a **financial thread** per patient per date of service — that every artifact attaches to, rendered as a vertical timeline on a single screen. The API is a byproduct of the object, not the point of it. Add one derived field that changes everything: *where is this stuck, and for how long, and for how much.* That field turns the chain from a record into a worklist generator.

**Alternate/better solutions.**
1. *Simpler mechanism*: a correlation identifier stamped on every record plus one timeline view. Ninety percent of the value at ten percent of the graph modeling, and it ships in the beta rather than after it.
2. *Event-sourced ledger*: model the cycle as an append-only event log per thread; the "query" is a fold over events. This also yields period close, reversal, and point-in-time reconstruction for free, and aligns with the subledger ambitions in the small-practice beta packet.
3. *Frappe-native shortcut*: lean on the platform's existing document, version, and link infrastructure rather than inventing a provenance store. The differentiator is the semantics and the view, not the storage engine — and building less storage means shipping the view sooner.

**Reframe.** *One thread per visit that money travels along, with a current position and a next action.* Provenance as a tracking number — a mental model every human already owns from package delivery, and one that makes "where is my money" answerable without training.

**Gaps it leaves.** Threads that split (one visit, two claims) or merge (one payment, many visits). Recurring monthly service codes with no visit to anchor to. Capitation, where money never traces to a specific visit at all. Refunds that cross threads. Corrections that rewrite history rather than appending to it. And the day-one problem: the beta customer arrives with 90 days of open AR migrated from another system, and the chain must present a clean boundary rather than a cliff of untraceable balances.

**Provenance twist check.** This *is* the twist, so the test is different: does it produce artifacts a biller touches daily? Three qualify — the one-click appeal packet, the auto-derived secondary claim, and the stuck-money worklist. If those three exist in beta, the chain is justified and the differentiation is real. If only the API exists, the differentiator is a paper claim and the build cost is unrecovered.

## SY-2 — Pre-service financial clearance

**Verdict: essential-core, and the most compelling product story in the packet.** Moving financial work from forty-five days after the visit to two days before it *is* the economic argument for openPractice, and it is the story a practice owner will repeat to another practice owner. It is also the piece that makes the front desk, rather than the biller, the leverage point — which suits role compression exactly.

**Make it more useful.** One screen: tomorrow's schedule, one clearance chip per appointment, one action per exception. Three chip states with a reason — never a numeric confidence score the front desk must interpret. The confidence score proposed in CA-1 should be an internal ranking mechanism that sorts the worklist, not a number rendered in the interface.

**Alternate/better solutions.**
1. *Simpler mechanism*: a printable "tomorrow's exceptions" list of three to eight rows. A small practice's morning huddle happens around a piece of paper; meet the workflow where it already is instead of asking it to move to a dashboard.
2. *Shift further left*: make clearance a precondition of *booking* for high-risk payer and service combinations, so the exception never reaches the day-before list at all.

**Reframe.** *Every patient arrives financially clear, or arrives with a known, priced, agreed exception.* Clearance is a state the appointment must reach — a preflight checklist — and the schedule should render un-cleared appointments as the anomaly rather than rendering cleared ones as an achievement.

**Gaps it leaves.** Same-day and walk-in appointments, which are precisely the ones that go financially wrong and which no pre-service sweep can catch. New patients with no coverage history. Plan changes between clearance and arrival. Services added during the visit. Self-pay clearance, which means Good Faith Estimate and deposit policy rather than eligibility. Workers' comp arrivals with no adjuster information. And the policy question the feat implies but never resolves: what happens when a patient declines to pay the estimated amount at check-in.

**Provenance twist check.** Real and high-value. The clearance record is the origin of the chain and the best possible defense in a dispute. Make it useful by producing the product's single best KPI: when a denial arrives, provenance can determine whether clearance was performed, skipped, or overridden — and therefore whether the denial was **preventable**. A monthly preventable-denial count with dollars attached is the number that proves the entire thesis, to the practice and to the market.

## SY-3 — Human-gated automation

**Verdict: questionable as a feat, though essential as a principle.** The constitution already mandates human authority over consequential financial action, so this is a ratified constraint rather than something to build. Listing it as a feat invites the construction of a generic approval framework, which in a two-person practice adds clicks to everything and produces the failure mode nobody models: queue rot, where gates create backlog and backlog costs more than the risk the gate prevented.

**Make it more useful.** Convert it from a framework into a **policy table**: four tiers (auto and reversible; proceeds with notification and undo; requires confirmation; requires two people), with every financial action assigned a shipped default. Then a small practice configures nothing, and the governance conversation becomes a one-page table rather than a workflow engine.

**Alternate/better solutions.**
1. *Simpler mechanism*: prefer **reversibility over approval**. If every automated action can be undone in one click and appears in a daily digest, far fewer gates are needed, and the ones remaining are meaningful. Approval gates cost a solo biller more than they protect at this scale.
2. *Gate on two axes only*: money leaving the practice and commitments leaving the building. Refunds, autopay charges, claim submissions, and appeals get gates; internal state transitions do not.

**Reframe.** *Humans confirm what cannot be undone; everything else is reversible and visible.* Replacing the approval question with a reversibility question eliminates most of the workflow while strengthening the actual control.

**Gaps it leaves.** What happens when nobody confirms — the dominant real-world risk in a two-person practice. After-hours and vacation coverage. Delegation. The gap between "confirmed" and "actually read." Timeouts and auto-escalation. And the direct conflict with timely filing: a gate that stalls a claim past its deadline destroys the money the gate existed to protect, which means every gate needs a deadline-aware escape.

**Provenance twist check.** Naturally strong — actor, evidence, and version on every consequential decision is exactly what provenance is for. Make it usable by rendering it as a **daily digest**: everything the system did or proposed yesterday, and everything waiting on a human, on one page, readable in five minutes. That is the small-practice-shaped face of governance, and it is also the thing that keeps queue rot visible.

## SY-4 — Payer accountability surface

**Verdict: questionable as a distinct feat — it duplicates CL-6.** At synthesis level it should be doing something the atomic cannot: federating payer performance across many openPractice deployments into a benchmark no single practice could compute. That is a genuinely different capability with a genuinely different value proposition, and it deserves its own name (see missing feats) rather than restating CL-6 one level up.

**Make it more useful.** Reserve the synthesis-level feat for the cross-deployment benchmark and its governance — opt-in, anonymized, aggregation-threshold-protected, with published metric definitions so results are reproducible and citable outside the product. That connects directly to the packet's open-metric-semantics differentiator and makes payer accountability a community asset rather than a dashboard.

**Alternate/better solutions.**
1. *Simpler mechanism*: publish the metric definitions and an export format; let a third party or the community aggregate. openPractice supplies semantics and data portability rather than operating a benchmark service, which avoids becoming a data custodian.
2. *Narrow the claim*: for beta, "payer accountability" reduces to two facts per payer — how long they take and how often they pay what they should — and the rest is deferred without loss.

**Reframe.** *Turn every deployment's remittance history into a shared public good about payer behavior* — that is a differentiator with no proprietary counter, because incumbents cannot publish payer performance without damaging their own network relationships.

**Gaps it leaves.** Aggregation privacy and re-identification risk with few contributors. Contribution incentives before the network is large enough to give back. Comparability across specialties and geographies. And the legal question of publishing payer-performance claims, which needs review before it becomes a marketing surface.

**Provenance twist check.** The benchmark is only credible if each contributed number is reproducible from a deployment's own chain. Make it useful by shipping the drill-down first: a practice must be able to see exactly which claims produced its own number before it will trust or contribute to a network one.

---

# Cluster-Level Review

## Missing feats

- **Secondary and tertiary claim derivation** — build the next payer's claim automatically from the posted primary remittance, including the prior-payer adjudication amounts the outgoing claim requires, plus crossover detection so the practice does not duplicate a claim the payer already forwarded. This is 15-25% of claim volume, is the packet's single best provenance application, and is currently named nowhere.
- **Corrected claims, voids, and replacement control** — replacement and void handling with the original payer control number, a diff of what changed, and duplicate-denial protection. Currently implied by a parity-floor phrase and owned by no feat, despite being one of the top three things a biller does after a denial.
- **Timely-filing and deadline control plane** — one clock per claim and per appeal, seeded from payer policy, with escalation before expiry and automatic classification when a deadline passes. Named as an adopted gap in the claims document but given no owner, no surface, and no interaction with the approval gates that can stall it.
- **Manual EOB and paper-check intake** — a structured path to enter a paper remittance and a paper or lockbox check that produces the same posting, variance, provenance, and statement objects as an electronic remittance. Without it, a meaningful share of small-practice cash bypasses the entire money chain and silently breaks every metric built on it.
- **Cash, deposit, and settlement reconciliation** — prove that posted equals banked by matching remittance deposit records, card-processor settlements, and drawer counts. The beta packet wants this; the revenue-cycle cluster does not own it, so it currently falls between two packets.
- **Recoupment and takeback handling** — payer offsets applied against future payments, post-payment audits, and the negative-balance and rebill work they create. This is the most confusing thing a small-practice biller encounters and it appears nowhere in the cluster.
- **Self-pay pricing and Good Faith Estimate compliance** — a self-pay schedule, compliant estimate generation with required elements and timing, retained delivery evidence, and the dispute-resolution path. A legal obligation, not a differentiator, and currently only flagged in the beta packet's prose.
- **Non-visit revenue capture** — time- and period-based services with no appointment to derive from (chronic care management, remote monitoring, e-visits, prolonged services). Every derivation feat in the cluster assumes a visit exists.
- **Workers' compensation, auto, and liability billing** — a distinct payer model with claim number, adjuster, date of injury, jurisdictional fee schedule, no eligibility transaction, and different statement rules. Assumed away throughout, and a single such patient breaks eligibility, COB, estimates, and statements simultaneously.
- **Provider enrollment gating** — hold charges for a provider not yet effective with a payer, and rebill automatically when enrollment backdates. Enrollment lives in the beta packet's administration cluster but must reach into charge and claim behavior to be worth anything.
- **Opening balances and financial migration boundary** — day-one AR, claims in flight, and patient balances from the prior system, with an explicit provenance boundary marking pre-openPractice history. The first beta customer has this problem on day one, and the money chain must degrade gracefully rather than present a cliff.
- **Denial-to-rule promotion** — every worked denial can promote a scrubber rule with one action, making the edit set an accumulating practice asset and, opt-in and anonymized, a shared community rule pack.

## Cross-feat gaps — where money falls through

1. **Charge posted, claim never created.** The missing-charge worklist stops at charge existence; nothing watches the charge-to-claim transition.
2. **Claim created, never submitted.** A batch built but not sent, or a claim held by a gate with no owner, is invisible to every feat in the cluster.
3. **Submitted, never acknowledged.** Silence is unmodeled — every acknowledgment feat describes what to do with a response, not with the absence of one.
4. **Remittance posted, variance never worked.** Posting closes the item; underpayment recovery has no owner in this packet at all, and is deferred to a different packet's cluster.
5. **Patient responsibility determined, statement never issued.** Nothing measures adjudication-to-statement latency, which is pure aging.
6. **Estimate given, never compared to actual.** Variance is mentioned in the twist but has no worklist, so estimate accuracy never improves.
7. **Authorization consumed, units never decremented.** The gate reads authorization state; nothing writes back from posted charges.
8. **Denial worked, scrubber never learns.** No loop exists between resolution and prevention, so the same denial recurs indefinitely.
9. **Payment collected, never allocated.** Unapplied cash has no owner, and it grows monotonically.
10. **Refund owed, clock never starts.** Credit balances have no age, therefore no compliance visibility.
11. **Coverage changed, downstream artifacts never invalidated.** Estimates and statements built on superseded coverage go stale silently and generate disputes weeks later.
12. **Posted, never reconciled to bank.** Deposit matching sits outside this packet, so the cluster can report revenue it cannot prove was received.
13. **Override recorded, outcome never measured.** Every override feat captures a reason; none captures what happened, so no rule ever gets corrected.
14. **No terminal state.** Nothing defines when an item leaves AR — write-off, collections handoff, or closure — so aging reports grow forever and the "stuck money" concept has no floor.

## Intuitiveness assessment

**What a solo biller's daily view should be.** One page, five stacks, ordered by money rather than by module:

1. *Tomorrow* — appointments not financially clear, with dollars at risk.
2. *Yesterday* — arrived visits with no billing disposition.
3. *Outbound* — claims held, rejected, or silent beyond 72 hours.
4. *Inbound* — remittance exceptions and denials, sorted by dollars descending.
5. *Two-way* — patient balances needing action, and credits needing refund with an age.

Nothing else should be on that page. Everything else in the cluster is a drill-down from one of those five.

**Where cognitive load concentrates.** Three places, all avoidable. First, **vocabulary leakage**: transaction and reason-code names (999, 277CA, CARC/RARC, PLB, 837 loops) must never surface as primary labels — they translate to "needs fix," "waiting," "paid short," "taken back," "already sent." Second, **configuration surfaces**: freshness windows, edit-rule authoring, cadence design, approval tiers, service-type code selection, and auto-post thresholds are six separate configuration screens across these four documents, each individually defensible and collectively fatal. Every one must ship with a default and stay hidden until the practice outgrows it. Third, **queue multiplication**: at least seven feats propose their own worklist. Seven queues in a practice with one biller means seven places to forget; they must collapse into the five stacks above.

**One mental model for money.** *Every visit is a package with a tracking number.* It moves through stations — cleared, charged, claimed, acknowledged, adjudicated, collected, closed. At any instant it sits at exactly one station, with an age, a dollar amount, and an owner. Work is defined as packages stuck at a station longer than they should be. Provenance is the tracking history. Denials are packages returned to sender. Estimates are the quoted shipping cost, and variance is the difference on delivery. Statements are the invoice for the portion the recipient owes. If the product can hold that single metaphor across all four atomic domains — one object, one position, one age, one number — it will feel more intuitive than any incumbent regardless of relative feature count, because incumbents organize by department and this organizes by the thing the user actually cares about.

## Top 3 reframings

1. **From "revenue cycle management" to "money in motion, with a stuck detector."** The core object is not a claim, a charge, or a statement — it is a per-visit financial thread with a station, an age, and a dollar amount. Every feature is either a station transition or a stuck-item worklist. This collapses twenty-eight feats into one model, gives the solo biller one mental model instead of four modules, and makes the fourteen cross-feat seams above impossible by construction, because a thread cannot sit between stations without being visible.

2. **From "provenance as audit trail" to "provenance as generator."** The chain earns its build cost only when it *emits* things: a one-click appeal packet, an auto-derived secondary claim, a preventable-denial count, a retroactive-repricing rebill list, a root cause for recurring refunds. Auditability is a byproduct that costs money; generation is a product that makes money. Every provenance investment should be justified by naming the artifact it produces and the minutes it saves — and if no artifact can be named, that link should store less.

3. **From "denial management" to "the practice's rejection memory."** Every rejection, denial, and override worked once must produce a rule, a default, or an upstream data fix that prevents its recurrence, and the scrubber must be scored by outcomes rather than by rule count. Success is a *shrinking* workbench and a falling recurrence rate — the inverse of how denial products are normally sold, and a claim no incumbent makes because their economics reward denial volume rather than denial extinction.

*Runner-up, worth holding:* **coverage as cache invalidation** — eligibility, coverage watch, estimate variance, and statement suppression are one mechanism (a cached payer assertion with a freshness policy and a downstream dependency graph), not four features.

## Relationships

Reviews [Synthesis: Revenue Cycle](../brainstorm/openpractice-feature-list-synthesis-revenue-cycle.md) and its atomic members: [Coverage And Authorizations](../brainstorm/openpractice-feature-list-coverage-and-authorizations.md), [Charge Capture And Coding](../brainstorm/openpractice-feature-list-charge-capture-and-coding.md), [Claims And Remittance](../brainstorm/openpractice-feature-list-claims-and-remittance.md), [Patient Billing And Payments](../brainstorm/openpractice-feature-list-patient-billing-and-payments.md). Context: [Feature List Overview](../brainstorm/openpractice-feature-list-overview.md), [Small-Practice Beta Overview](../brainstorm/openpractice-small-practice-beta-overview.md).
