# Small-Practice Beta Scope And Sequencing — Brainstorm

Status: brainstorm
Kind: plan
Summary: the beta is rebuilt as six slices that carry one auditable loop from a fax or a phone call through a booked billable appointment, a charge, a claim, a remittance, a bank line, and a closed day — with the day rituals and the Thread contract first, the self-pay and Good Faith Estimate money path before any claim, the enrollment-gated claim hold and promise memory inside the claim slice, and the bank-line inbox where the money finally ties; every slice names the differentiator it advances so that a headline capability can never again belong to no slice, acceptance requires one live ERA and one live merchant settlement rather than test traffic, and cutover is replaced by progressive activation over an open-items-only migration with legacy AR running off as a watched thread.
Topics: openpractice-small-practice-beta, beta-scope, feature-roadmap, practice-strategy
Repository context: openPractice — proposed release slicing for the first small ambulatory practice beta
Captured: 2026-08-27

## Revision note

This document was revised in place on 2026-08-27 against two binding inputs: the
eleven ratified framing decisions in
[Framing Decisions](../review/openpractice-framing-decisions.md) (FD-1 through
FD-11, all ratified 2026-08-27), and the PASS-WITH-AMENDMENTS verdict in
[Paper Prototypes](../review/openpractice-paper-prototypes.md) (amendments A-1
through A-4, clarifications C-1 through C-3, naming fix N-1). Both derive from
[Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md), whose
§6.1 found the most serious defect this revision exists to fix: **the packet's
headline revenue-integrity differentiators mapped to no release slice at all**,
so the differentiation was becoming post-beta silently, without a decision ever
being taken.

The revision does four things. It rebuilds the slices so that every
beta-blocking capability has a named home. It makes each slice state which
differentiator it advances, and adds a coverage table so an orphaned
differentiator is visible at a glance rather than discoverable only by tracing.
It replaces the test-traffic escape with live-money acceptance (FD-8) and the
cutover project with progressive activation (FD-9). And it fixes the vocabulary:
every capability named in a slice below is defined in
[Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md),
which is now the normative frame — the six-noun work model, the party register
that model presupposes, and the eight-rule feature contract (FR-1 … FR-8) that
the slices exercise.

Status remains `brainstorm`. Contradiction remains legal across the packet, but a
contradiction with FD-1 … FD-11 is now a defect rather than a variant.

## Possible feats

- **Thin complete slice** — demonstrate one real administrative and financial story end to end instead of many disconnected screens.
- **Every slice carries a differentiator** — no slice is pure plumbing, and no headline claim is left without a slice that advances it.
- **Manual-safe beta** — support governed staff completion when a partner API, automation, or patient channel is unavailable, through the manual connector rather than through workarounds outside the system.
- **One-connector discipline** — one EHR, clearinghouse route, payment processor, and communication provider, behind contracts that anticipate replacement without building a marketplace.
- **Live money, not test traffic** — one real ERA and one real merchant settlement, because the mechanisms that matter only misbehave with real data.
- **Progressive activation** — six "firsts" replace a cutover event, and legacy AR runs off where it already lives.
- **Risk-based deferral** — postpone capabilities whose safe value depends on volume, hardware, historical data, or a deterministic workflow not yet proven.

## Focus

What is the smallest coherent beta that a small group practice can operate
meaningfully, in what order should foundations and workflows be proven, and what
must be true — with real money, at a real practice — before the beta counts?

## What the beta must prove

Six claims. Each is a thing an incumbent at this price point does not do, and each
is stated as something a person at the practice experiences rather than as an
architecture.

| # | Differentiator | Said plainly |
|---|---|---|
| **D-1** | **Readiness is the sort order of the day** | Nothing that costs money waits unseen, and nobody has to remember to check anything. |
| **D-2** | **Promise memory** | The practice knows what each payer actually pays it, without ever loading a contract — and is told when that changes. |
| **D-3** | **Provenance as artifacts** | The appeal packet, the secondary claim, the retro-release list, and undo — the chain is sold as work it does, not as a trace it keeps. |
| **D-4** | **Every bank line has an owner** | The owner can open his own banking app and find every line explained by exactly one thing. |
| **D-5** | **The close is the evidence** | The day closes with a signature, and the binder accumulates one sheet at a time from work that had to happen anyway. |
| **D-6** | **The channels nobody else owns** | A fax and a phone call become tracked work with a clock and an owner, instead of a pile on the counter. |

**These six may not be silently deferred.** A differentiator with an empty row in
the coverage table below is a defect in this document. Removing one from beta
scope requires deleting it from this list with a stated reason — never letting it
fall out of the slices.

## How to read the slices

This is a sequencing hypothesis, not an implementation task list. OpenSpec governs
the selected product changes; the eventual Speckit feature owns executable work.

Every slice states five things: what it delivers, which differentiator it advances,
which of the six surfaces it lights up, which feature-contract rules it exercises,
and what proves it. Capability names in **bold** are defined in the capability
register of
[Feature Normalization](openpractice-small-practice-beta-feature-normalization.md);
a slice may not reference anything that register does not name.

One structural rule runs through all six: **the End-of-Day Close sheet is the
beta's integration test.** It ships in Slice 0 with only the sections the product
can populate on day one, and every later slice adds exactly one section to it. If
a slice cannot add its section to the close sheet, the slice is not done.

---

## Slice 0 — The practice, the Roster, and the Day

The foundation slice, rebuilt. Its job is not configuration; it is to make a day
openable and closable, to write the identity everything else attaches to, and to
start the two enrollments that pace the whole beta.

**Delivers**

- the **party and place register** — patients, households, guarantors, subscribers, referring offices, payers, vendors, locations and their hours — established as substrate (A-4), and seeded per FR-8 from the practice's own W-9, a prior CMS-1500 or 837, one remittance, last year's charge lines, and NPPES lookups rather than authored;
- **The Roster** — humans, robots, and the external biller (FD-1) — as the entire access-administration surface, with **Same-Day Offboarding Switch**; capacity is rendered from it (C-1), so no Schedule object is invented;
- **The Thread Contract**, written and versioned **before any consumer** (FD-7): what it identifies, that it is minted at intent (A-3), the five properties including the derived clock (A-2), how children inherit, how re-association works when a partner strips the identifier, what "unknown" looks like, and behavior on merge, split, and the migration boundary — with the non-visit types enumerated, including **Old-System Balance Thread**;
- **Morning Huddle Page** and **End-of-Day Close** as the frame of the day, printed to one sheet, signed;
- the four governance tiers as shipped, non-authorable behavior (FR-6), plus **Practice Change Log With Undo** and **Scheduled Changes Calendar**;
- **Code-Set Core And Licensed Overlay Import** at first run (FR-7): license-free core in the repository, the practice's own CPT overlay imported from its licensed source;
- **Immutable Money Ledger And Reason Taxonomy** — the taxonomy defined *before anything posts*, because it is the join key to the general ledger and is unfixable downstream;
- **Credential And Certificate Expiry Radar**, **BAA-Gated Vendor Activation**, **Test/Production Firewall**, **Support Access With Consent** — all four are beta-blocking precisely because FD-8 puts real patients and real money in the beta;
- **Progressive Activation And The Six Firsts** begins; **Open-Items Migration And Balance Reconciliation** is planned and its signed sheet designed; **Remittance History Import (Evidence Only)** runs where the legacy system can export it, behind an explicit pre-openPractice provenance boundary;
- **kickoff threads:** clearinghouse enrollment and merchant enrollment open on day one and are tracked as the implementation's first two threads (FD-8).

**Differentiators advanced:** **D-5** — a day can be opened and closed with a
signature before there is a single dollar in the system, and that signed sheet is
already the first page of the binder. **D-3** begins here too: the change log with
undo is the first generated provenance artifact, and undo is the reason the chain
must be complete.

**Surfaces:** The Practice; Today (skeleton).
**Rules exercised:** FR-1, FR-4, FR-5, FR-6, FR-7, FR-8.
**What proves it:** a day opens on a printed sheet and closes with a signature;
the Thread contract is versioned and published before any slice-1 capability is
staged; both partner enrollments have submitted applications with dated
acknowledgments.

---

## Slice 1 — The door: phone, fax, the Pile, and a booked billable appointment

The channel slice. It exists because the system diagram promised telephone and fax
and no atomic owned either, which made every queue and every accountability number
structurally wrong from week one (FD-10a).

**Delivers**

- **Phone Console And Call Disposition** — screen-pop, one-click book/message/callback, a required disposition, and a five-second capture from any screen so the parallel paper list never starts;
- **One Inbound Pile** — fax, upload, scan, and payer mail as one aging inbox where every page becomes a Thread on arrival with a station, an owner, and one disposition (C-2), plus **Confirmed Fax Destination Registry** and **Misdirected PHI Disposition**;
- **Referral-In As An Entry Point** — the inbound referral from arrival through match to booking and a closed-loop reply the way the sender sent it;
- scheduling as Thread minting at intent, against capacity rendered from the Roster, with **Reschedule As Carry-Forward** and **Walk-In And Same-Day Access**;
- the **Readiness** evaluator, once, with its three severities and the standing rule *hold the claim, never the patient*; its verdicts are consumed here by the desk wave-through and the booking warning, and in Slice 3 by the claim hold;
- coverage as a declared **Promise**: one eligibility route with the raw response preserved, and explainable work for inactive, stale, ambiguous, or failed evidence; authorization with units, expiry, and its derived clock;
- **Desk-Side Real-Time Exception**, **Assisted Mode**, **Snooze With A Reason**, **Urgent-Content Safety Property**, **Patient Merge Aftermath And Reversal**;
- the visit task list with a URL that needs no account, closable by staff on the patient's behalf; kiosk reuse only if the design partner needs it.

**Differentiators advanced:** **D-6** (a fax becomes a booked appointment without
retyping) and **D-1** (the huddle page's sort order is readiness, and the fourteen
who are fine are countable rather than readable).

**Surfaces:** The Pile; Today; The Patient; The Thread.
**Rules exercised:** FR-1, FR-2, FR-3, FR-5, FR-8.
**What proves it:** the first acceptance demo — *a fax landing to a booked billable
appointment in two minutes with zero retyping* — and a huddle page that names every
patient who needs a person before they arrive, with the one-sentence fix beside
each.

---

## Slice 2 — Money at the desk: self-pay, the Good Faith Estimate, and the pay link

Promoted from nowhere (§6.3 found no estimate, no GFE, no time-of-service
collection, and no self-pay path in any prior slice) to here, ahead of claims, for
two reasons. It is the **simplest** money path — no payer, no clearinghouse — so it
is the natural first proof of the money model. And it is the only way the beta
collects a dollar before it can bill anyone (FD-4).

**Delivers**

- **Self-Pay And Time-Of-Service Money Path** — priced, discountable, receipt-bearing, with a GL destination and the accounting distinction between a courtesy discount and a write-off; estimate → GFE where required → collect at the visit → receipt → Thread closed the same day;
- **Good Faith Estimate Register And Dispute Clock** — issue, version, deliver, retain; watch billed-versus-estimated variance; assemble the dispute file at the threshold. Beta estimates are schedule-of-charges arithmetic plus observed-Promise copay and deductible hints — not benefit-engine modelling;
- **Price-By-Date-Of-Service**, so a mid-cycle price change never reprices a prior estimate and stale estimates surface as work before the patient is billed;
- **Patient Money View** — balance, why, and pay-now behind a texted link with no account required (FD-10b);
- the **Money Line** in all three of its states (C-3): the card batch is *expected* at 5:20pm and *landed* two days later; the counted drawer is landed immediately;
- **Daily Balanced Day** inside the Close — float, cash counted, over/short, card batch, posted receipts — and the **deposit slip the product generates**, because if openPractice defines the deposit, the deposit matches by construction;
- **Credit-Balance Clocks By Cause** seeded with its first cause (we over-collected at the desk) and **Refund Disbursement Register** at governance tier 4.

**Differentiators advanced:** **D-4** in its first and simplest form — the drawer
and the batch tie at 5pm on day one — and **D-5**, because the close sheet gains its
money section here.

**Surfaces:** The Patient; Today; The Money (first section).
**Rules exercised:** FR-2, FR-3, FR-5, FR-6.
**What proves it:** a cash patient is estimated, given a signed GFE, collected from,
receipted, and closed the same day; the drawer and the terminal batch reconcile on
the close sheet with the difference shown *before* it was resolved, so the sheet
proves the check ran rather than asserting it.

---

## Slice 3 — The claim and the promise

The revenue loop, with the two capabilities the previous sequencing left homeless
placed inside it rather than after it.

**Delivers**

- charge creation from a completed-event or EHR charge-derivation reference, under the core-plus-overlay code catalog, with **Waiting-On-Clinical State** so an unbilled visit aging toward a filing limit does not vanish from both systems;
- **Enrollment-Gated Claim Hold And Retro-Release** — hold, not reject and not submit, any claim whose rendering provider is not effective with that payer at that location on that date of service; on approval with a retroactive effective date, release every eligible held claim **in one action** and flag the rest for self-pay, appeal, or write-off. This is the valve that Slice 1's enrollment readiness verdict was computed for (FR-3), and the highest-dollar administrative capability in the packet;
- **Substitute And Supervising Billing Arrangements** — locum with its day countdown, reciprocal billing, incident-to, split/shared — because without them the readiness evaluator is confidently wrong;
- one 837P path: scrub, submit, acknowledge, status, correct — with **Corrected Claims, Voids, And Replacement Control** carrying the original payer control number;
- **Timely-Filing And Deadline Control Plane**: one clock per claim and per appeal, seeded from payer policy, rendered as days *remaining*, with the deadline-aware escape from every gate (FR-6);
- 835 ingestion and reversible service-line posting, plus **Secondary And Tertiary Claim Derivation** built from the posted primary — 15–25% of claim volume and the single best application of the provenance chain;
- **Learned Payer Baseline** — the observed Promise (FD-2), computed as a median by payer × code × modifier × place of service with n and spread, from the beta's own posted remits plus the imported history seed, activating progressively as n grows rather than pretending confidence at n=1;
- **Payer Drift Alarm** as a weekly digest of two or three payer behaviors, never a queue of claim lines; known-benign classes suppressed by name rather than by tolerance;
- **Appeal Packet**, **Payer Records-Request Tracker**, and **Payer Follow-Up By Batch**.

**Differentiators advanced:** **D-2** (the practice forms an expectation without
ever loading a contract) and **D-3** (the appeal packet, the auto-derived secondary,
and the one-click retro-release list are the chain doing work). **D-1** continues:
held claims and stuck threads sort into the day by dollars and clock.

**Surfaces:** The Thread; Today; The Money.
**Rules exercised:** FR-2, FR-3, FR-4, FR-6, FR-7, FR-8.
**What proves it:** one **live** ERA posted end-to-end (the first half of FD-8);
a held-claim bucket released retroactively in one action with a dollar total on the
screen; a weekly drift digest whose three rows a physician-owner reads without
asking what a row means.

---

## Slice 4 — The money ties

The slice the reviewers said must never be the one that gets cut when Slice 3 runs
long. Reconciliation to a bank line cannot be simulated later, and its absence
makes every other number in the product unverifiable.

**Delivers**

- **Bank Line Inbox** — the bank statement as a to-zero worklist where every credit and debit is explained by exactly one settlement, fee, transfer, or named non-patient item, and only landed-and-unexplained is red;
- **Manual EOB And Paper-Check Intake**, producing the same posting, variance, and provenance objects as an 835, because paper remains a meaningful share of small-practice payer volume;
- **Payer-Level Adjustment (PLB) Ledger** — takebacks, forwarding balances, interest, capitation, early-payment allowances — without which the check never equals the sum of claim payments and every affected deposit fails to reassociate for reasons nobody can see;
- **Merchant Settlement, Fee, And Chargeback Reconciliation** — gross versus net funding, per-batch and monthly fees, reserve holds, chargebacks and their fees, returned items, each with an expense or liability destination;
- **Recoupment And Overpayment Case File** — demand letter → appeal-or-agree → refund or offset election → the specific future remittance line where the offset lands;
- **Credit-Balance Clocks By Cause** completed, with days remaining and the "why do we have this" classifier;
- **Money-Day Predictor** as the sum of expected Money Lines — arithmetic, not a forecast;
- **Accounting Export, QuickBooks-Shaped** with **Export-Batch Freeze** (FD-3): one journal entry per bank deposit, cash-basis default, at most eight mapped accounts, and a one-page reconciliation certificate; the books are always closed and month-end is printing;
- the owner's weekly money page on The Money — did it come in, did they pay what they usually pay, where is it stuck, does it tie — with the **Old-System Balance Thread** shown as whole-practice truth, and **The Daily Five And Monthly Six** with visible denominators.

**Differentiators advanced:** **D-4** in full (this is the slice the claim is
named for), **D-2** rendered as three variance rows the owner can act on, **D-5**
as the close sheet's last section, and **D-3** as the reconciliation certificate
and the export batch's back-references.

**Surfaces:** The Money; Today.
**Rules exercised:** FR-2, FR-3, FR-4, FR-5.
**What proves it:** the second acceptance demo — *Friday's money ties to the bank,
and the office manager can see why in one screen* — and one **live** merchant
settlement reconciled to a bank line (the second half of FD-8).

---

## Slice 5 — Run it without us

Exception readiness and adoption fit, rewritten from rehearsal to residency. The
question is no longer "can we demonstrate the loop" but "can they run it on a
Thursday when the office manager is out and the clearinghouse is silent".

**Delivers**

- **Silent Failure Detector** with per-detector canaries — alerting on the *absence* of expected activity, because a broken integration otherwise produces an empty queue and a healthy metric at the same time;
- **Upgrade Safety Rail**, **Recovery Day**, and the **Data Trust Panel** beside the numbers;
- **Right Of Access Clock** and **Compliance As A Dated To-Do List**, with the **Recurring Practice Calendar** shipped pre-populated so the practice inherits an operating rhythm (FR-5);
- **Coverage Day And While-You-Were-Out** and **Provider Absence And Mass Schedule Disruption** — the unplanned 7:40am sick call and the day twenty-two patients have to be moved;
- **Practice Health One-Pager** emailed monthly, its footer naming the metric-pack version;
- all six firsts green; the **Open-Items Migration And Balance Reconciliation** sheet signed; the legacy read-only period defined and the run-off visibly shrinking on the money page.

**Differentiators advanced:** **D-5** (the quarterly binder is now the accumulated
close sheets plus the restore certificate) and **D-1** (the negative form of
readiness — everything that will not be true in fourteen days, each line with a
name on it).

**Surfaces:** The Practice; Today; The Money.
**Rules exercised:** FR-1, FR-3, FR-5, FR-6.
**What proves it:** the practice operates for two consecutive weeks with no
product-team edits to production data and no bypass of normal authority; a restore
drill produces a signed certificate with a wall-clock RTO; a staff member who lacks
a smartphone or documents is not stranded anywhere in the loop.

---

## Differentiator coverage

An empty cell in the "advanced by" column is a defect in this document.

| Differentiator | Advanced by | Primary slice |
|---|---|---|
| D-1 Readiness is the sort order | 1, 3, 5 | Slice 1 |
| D-2 Promise memory | 3, 4 | Slice 3 |
| D-3 Provenance as artifacts | 0, 3, 4 | Slice 3 |
| D-4 Every bank line has an owner | 2, 4 | Slice 4 |
| D-5 The close is the evidence | 0, 2, 4, 5 | Slice 0, completed in 4 |
| D-6 The channels nobody else owns | 1 | Slice 1 |

## Feature-contract rule coverage

Every rule in
[Feature Normalization](openpractice-small-practice-beta-feature-normalization.md)
Part 2 is exercised by at least one slice. An unexercised rule is either dead or
the slices are wrong.

| Rule | Exercised by |
|---|---|
| FR-1 frame (2–4 staff + external biller) | 0, 1, 5 |
| FR-2 naming (no architecture vocabulary on a surface) | 1, 2, 3, 4 |
| FR-3 valve (every verdict names its consumer) | 1, 2, 3, 4, 5 |
| FR-4 artifact (every provenance link names what it emits) | 0, 3, 4 |
| FR-5 rhythm (day / week / month / quarter / year) | 0, 1, 2, 4, 5 |
| FR-6 governance tiers with deadline escapes | 0, 2, 3, 5 |
| FR-7 licensing (license-free core, licensed overlay) | 0, 3 |
| FR-8 learning (own artifacts before authored config) | 0, 1, 3 |

## Where the previously homeless capabilities landed

The six capabilities that were beta-blocking and belonged to no slice — the finding
that forced this revision — now each have exactly one home.

| Capability | Slice | Why there |
|---|---|---|
| **Enrollment-Gated Claim Hold And Retro-Release** | 3 | It is the valve on a verdict Slice 1 already computes; it cannot precede a claim path. |
| **Learned Payer Baseline** + **Payer Drift Alarm** | 3 | It is a median over remits the slice itself generates; it costs a query, not a contract project. |
| **Self-Pay And Time-Of-Service Money Path** + **Good Faith Estimate Register** | 2 | The simplest money path, and the only one that collects before the beta can bill. |
| **Bank Line Inbox** (with paper EOBs, PLB, merchant fees) | 4 | It needs claim money, desk money, and merchant money to already exist to reconcile against. |
| **End-of-Day Close** + **Morning Huddle Page** | 0, extended every slice | The day frame must exist before there is anything to close; each slice adds one section. |
| **Phone Console And Call Disposition** + **One Inbound Pile** | 1 | They are how work enters the building; every later count is wrong without them. |

## The first staged OpenSpec proposal

The slices above are a **build order**. The first thing staged to OpenSpec is not a
slice and not a domain — it is **one vertical thread** cut through Slices 0–4 at a
depth of one:

> **A fax or a phone call in → a booked, ready, billable appointment → a charge → a
> claim → a remittance → a bank line → a closed day.**

One referral, one patient, one payer, one provider, one location, one clearinghouse
route, one processor. No breadth anywhere: the point is that the seams between
domains — which is where the design will be wrong — are exposed before breadth
accumulates on top of them.

Two acceptance demos carry the whole product story and are the proposal's exit
criteria:

1. **"A fax landing to a booked billable appointment in two minutes with zero retyping."**
2. **"Friday's money ties to the bank, and the office manager can see why in one screen."**

The Thread contract (FD-7) is written and versioned **before** this proposal is
staged, not inside it. It is the one artifact with no consumer and no user, and it
is the thing four separate spines will otherwise be built against.

## Beta acceptance

**Test traffic proves nothing about money.** It proves plumbing. The two hardest
external dependencies — clearinghouse enrollment and merchant settlement data — are
exactly where healthcare betas die, and deferring them defers discovering that the
product does not work. FD-8 retires the test-traffic escape.

The beta counts when all of the following are true:

- **one live ERA posted end-to-end** — received, reassociated, posted to service lines, variance evaluated against the observed Promise, and reflected on a patient balance;
- **one live merchant settlement reconciled to a bank line** — gross versus net understood, the fee booked to an expense destination, the bank line explained;
- both of the acceptance demos above run at the practice, on the practice's own data;
- the six firsts are green and the day has closed with a signature on each of the last ten business days;
- staff can see and resolve every failure without database access, and a patient who lacks a smartphone or documents is not stranded;
- the constitutional gates hold: security review for everything touching PHI, money, authorizations, or credentials; contract tests at every integration boundary; calculation tests with boundary and failure cases; restore evidence; and a governance tier assigned to every action that moves money or leaves the building.

**Clearinghouse and merchant enrollment start at kickoff**, tracked as the
implementation's first two threads. Both are partner-paced and take weeks. If either
slips, the beta exit *date* moves; the acceptance criteria do not.

Test traffic remains acceptable — and preferable — for everything upstream of the
money: eligibility rehearsal, claim construction, scrubber rules, and the whole of
Slice 1.

## Progressive activation replaces cutover

There is no cutover project, no control room, no configuration-promotion pipeline,
and no policy-simulation harness (FD-9). There is a sequence of **six firsts**, each
activated when the previous one has succeeded once:

1. the first appointment booked in openPractice;
2. the first eligibility response received and read;
3. the first charge created from a real visit;
4. the first claim accepted by the clearinghouse;
5. the first remittance posted;
6. the first day banked, balanced, and closed with a signature.

**Migration is scoped to open items only:** future appointments, active patients on
the schedule, and open balances as summary Threads. Rejects arrive as work items
with owners, never as a report. A **signed one-page balance reconciliation** is the
gate, and the practice signs it — full AR conversion is the classic implementation
killer: highest effort, highest error rate, lowest durable value.

**Legacy AR runs off in the legacy system by default**, and is visible here as the
**Old-System Balance Thread** — count, oldest age, dollars, and who works it — so the
owner's money view shows whole-practice truth and the run-off is watched work rather
than invisible work.

One distinction this revision draws that the ratified decisions did not: **operational
migration and evidence import are different things.** Open-items-only governs what the
practice *works* in openPractice. **Remittance History Import (Evidence Only)** is a
read-only import of prior EOB and ERA history used solely to seed the observed
Promise, behind an explicit pre-openPractice provenance boundary, and it never posts
to the ledger. Without that distinction, FD-9's scoping would starve FD-2's cold
start; with it, both hold.

## Post-beta by default

- the native companion app, its adoption campaigns, and cross-backend federation — **but not the patient money view**, which is beta scope in Slice 2 (FD-10b);
- generated AI intake answers, predictive no-show and denial models, and learned routing — each deferred behind its prerequisite data, because denial prediction cannot be meaningful before denial-disposition history exists;
- geofence and Bluetooth arrival sensing beyond manual, QR, kiosk, SMS, or explicit confirmation;
- the contract-loading engine, the underpayment workbench, and the contract performance view — replaced in beta by the observed Promise and the drift digest, with **Contract-As-Arbiter Import** available per payer for the one payer the practice most distrusts;
- broad clearinghouse, payer, EHR, processor, and communication connector catalogs;
- enterprise organization hierarchies, billing-service tenancy, delegated credentialing, advanced workforce management, and hospital-inpatient billing;
- the permission-matrix editor, access-review campaigns, accounting of disclosures, the interface operations console, and the public API program;
- FQHC-specific sliding-fee and UDS packages unless the design partner belongs to that segment;
- network benchmarking, complex forecasting, autonomous coding, and autonomous consequential financial action.

Deferral is not rejection. Each candidate stays linked to its research and returns
when its prerequisite deterministic workflow, consent model, evidence, and evaluation
data exist.

## Open risks

| # | Risk | Action, and who |
|---|---|---|
| **R-1** | **Licensed IP (FD-11).** AMA licensing terms for practice-held CPT rights are not verified. If practices cannot lawfully import their own CPT content into a self-hosted product, the onboarding flow and possibly the charge-capture design change. | **Counsel verification before Slice 0's code-set import is staged.** Until it returns, the repository ships license-free content only and the overlay is loaded manually by the practice. Owner: product owner. |
| **R-2** | **Governance tier 3 versus the constitution.** Principle II requires "explicit human approval flows" for autonomous financial or patient-impacting action; FD-5 tier 3 sends claim batches and statements after a visible cancel window with no affirmative click. | Either amend the constitution or record the reading in the first proposal's Constitution Check. A brainstorm document cannot amend a constitution. Deliberately unresolved here. |
| **R-3** | **Live money means real PHI in the beta.** Retiring the test-traffic escape puts real patients into the product from Slice 1. | Test/Production Firewall, BAA-Gated Vendor Activation, and Support Access With Consent move into Slice 0 and are beta-blocking. The constitution's data gate still bars real PHI, credentials, or account data from any commit — which constrains fixtures, logs, and provenance retention. |
| **R-4** | **Cold start for promise memory.** At go-live n=0, and FD-2 puts the learned baseline on the critical path. | Seed from imported remittance history where the legacy system can export it; use the public Medicare fee schedule as a reference band; activate variance progressively as n grows. **If the legacy system cannot export remittance history, D-2 is demonstrable only late in the beta** — measure it, do not assert it. |
| **R-5** | **Partner-paced enrollment.** Clearinghouse and merchant enrollment are weeks long and outside the team's control. | Start both at kickoff as the first two implementation threads; report their status in every weekly review; treat a slip as a date change, never as an acceptance change. |
| **R-6** | **Append-only integrity versus required deletion.** Retention limits, minors' rules, patient-requested restrictions, and data minimization all require deletion from a structure defined as immutable, and no ratified decision covers it. | Decide it in the ledger design inside Slice 0, before the chain has a year of history to invalidate. **Deliberately unresolved and flagged.** |
| **R-7** | **The EHR boundary is specified in one direction.** Which system is authoritative for the appointment, what happens during an EHR outage, and how a corrected date of service or diagnosis drives a corrected claim are all implicit. | Settle with the design partner before Slice 3; every provenance chain otherwise terminates at "the EHR said so", which is where real disputes live. |
| **R-8** | **The HealthLinc name** is a registered mark of an existing community health center. | The app is deferred, so this does not block the beta — but the name must not appear on the patient money view or the no-account link. |

## Interfaces and boundaries

Consumes the launch archetype, the normalized capability register, existing
feature-list research, connector realities, the security constitution, the ratified
framing decisions, and design-partner evidence. Emits release boundaries and beta
outcome hypotheses for future OpenSpec proposals and Speckit features.

It does not create implementation tasks, promise a release date, select vendors, or
make deferred capabilities undesirable. It does now authorize production payer and
merchant traffic as a beta *requirement* (FD-8), which is a change from the prior
revision and carries R-3 with it. A beta is not permitted to bypass audit,
authorization, reversibility, or PHI safeguards merely because its user count is
small.

## Alternatives and tensions

An access-only beta is easier to deliver but cannot validate the product's
revenue-cycle thesis. A claims-only beta avoids patient-channel work but leaves
registration and scheduling context dependent on external re-entry. A complete thin
slice has more integration risk, yet it exposes foundational mistakes before breadth
accumulates. One connector per class accelerates learning but may accidentally
hard-code partner assumptions; explicit internal contracts and failure tests are the
countermeasure. Manual fallbacks increase safety and launchability, but they must be
measured so temporary work does not become permanent hidden labor.

Three tensions arrive with this revision.

**Six slices is more sequencing structure than four, for the same beta.** The
justification is that the four-slice version hid an entire differentiator; a slice
boundary that forces a capability to declare its home is worth the extra boundary.

**Slice 2 delays the claim path by a slice.** That is deliberate: a practice that
collects at the desk on day thirty is better off than one that bills correctly on day
sixty, and the money model is cheaper to get right without a payer in it.

**When Slice 3 runs long — and it will — cut the contract-arbiter import, never the
reconciliation.** Reconciliation to a bank line cannot be simulated later, produces
the weekly felt value, and its absence makes every other number unverifiable.
Expectation can arrive as a statistic over history the beta has by then accumulated,
and it will be better for having waited.

## Open questions

Closed by ratification and no longer open: whether certified test traffic may
substitute for live payer exchange (no — FD-8); whether the companion app is a
strategic must-have for beta (no, but the patient money view is — FD-10); whether
referral intake is essential to the first daily workflow (yes, and fax is the channel
— FD-10); and whether estimates and the GFE are in scope (yes, Slice 2 — FD-4).

Still open:

- Which exact EHR, clearinghouse, processor, communication provider, and accounting destination define the first contract-test suite?
- Which beta outcomes are mandatory for the first external user versus a later beta milestone, given that FD-8 fixes the floor but not the ceiling?
- Which specialty-specific administrative rules are necessary without importing clinical logic?
- Can the design partner's legacy system export remittance history at all (R-4), and in what form?
- What is the minimum viable form of the manual connector such that fax, paper, and payer-portal work keep the same status surface as automated exchange?
- How long is the legacy read-only period, and who decides the run-off is finished?

## Relationships

Combined with [Small Ambulatory Practice Beta Archetype](openpractice-small-practice-beta-launch-archetype.md)
and [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md)
in [Synthesis: Small-Practice Product Shape](openpractice-small-practice-beta-synthesis-product-shape.md).
It sequences the capabilities related by
[Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md)
and [Synthesis: Revenue Integrity And Financial Close](openpractice-small-practice-beta-synthesis-revenue-integrity.md).

Revised against the ratified [Framing Decisions](../review/openpractice-framing-decisions.md)
and the [Paper Prototypes](../review/openpractice-paper-prototypes.md) verdict, both
derived from [Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md)
§6 and §7. The sequencing contradiction this revision exists to fix is documented in
[Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md)
§"The structural finding"; the day rhythm the slices hang from is from
[Operations And Intelligence](../review/openpractice-feat-review-operations-and-intelligence.md).
The three printed pages in the prototypes document are the visual acceptance target
for Slices 0, 2, and 4.
</content>
