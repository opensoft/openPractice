# Enrollment-Gated Billing: Holds, Retro-Release, And Substitute Arrangements — Brainstorm

Status: brainstorm
Kind: report
Summary: participation is not a status anyone reads but a dated precondition the claim itself dry-runs — billing entity × rendering identity × payer product × location × date of service, returning five answers rather than two — so one computation warns at booking and holds at claiming; a claim for a pair that is not yet effective enters `charged, held` with a machine-readable release condition instead of being rejected or submitted, an approval carrying a backdated effective date releases every matching held claim in one re-scrubbed action while forcing a named disposition on the remainder that falls before it, a three-rung deadline escape puts substitution ahead of filing-for-the-clock ahead of deciding and refuses to fire at all where the enrollment case was denied, locum tenens Q6 with its continuous-day countdown, reciprocal Q5, incident-to's administrative half, and split/shared resolve *which* identity the precondition is tested against so the verdict is not confidently wrong, and the onboarding runway's first-billable date per payer — learned from the practice's own prior approvals, including whether that payer backdates at all — has a departure mirror that terminates participation with a date rather than deleting it.
Topics: openpractice-small-practice-beta, enrollment-gated-billing
Repository context: openPractice — the enrollment-gated billing capability for the first small ambulatory practice beta (Slice 3, beta-blocking)
Captured: 2026-08-27

## Possible feats

- **Participation as a claim-time precondition** — a dated tuple test (billing entity × rendering identity × payer product × location × date of service) returning five answers rather than two, dry-run from the claim itself so the booking warning and the claim hold can never disagree.
- **The hold that is never a rejection** — a claim for a pair that is not effective enters `charged`, `held`, with a structured, queryable release condition and a named owner; it is never auto-submitted, never discarded, and its reason is never free text.
- **Retro-release in one action, and a remainder nobody may leave behind** — an approval with a backdated effective date releases every eligible held claim at once, re-scrubbed against today's facts rather than replayed, and forces a named three-way disposition on the claims that fall before it before the enrollment case may close.
- **The deadline escape, laddered** — substitute the identity first, file for the clock second, decide third; a pending case may escape into submission, a denied one may not, and no rung ever fires silently.
- **Billable under whom** — locum tenens (Q6) with its continuous-day countdown, reciprocal billing (Q5), incident-to's administrative half with a physician attestation, and split/shared, modelled as dated arrangement Promises that change which identity the precondition is tested against.
- **The onboarding runway and its departure mirror** — a backwards-planned first-billable date per payer computed from the practice's own prior approvals, including the learned fact of whether that payer backdates at all; and a departure that terminates participation with a date instead of deleting it, so a year of correct claims stays correct.
- **One computation, two valves** — booking consumes the verdict as a warning that never blocks, claiming consumes it as a hold that never submits, and The Practice renders the same function in negative form fourteen days ahead.

## Focus

Between "we booked her anyway" and "the payer denied it forty-five days later" there is a bucket of money nobody owns. What is the smallest set of facts that lets openPractice hold exactly the claims that must not go out, release all of them the moment an effective date lands, tell the practice on Thursday which of Tuesday's appointments are unbillable and under whose name they *could* be billed instead — and never destroy the money it is protecting by holding past a filing deadline?

## Current state: the verdict that had no valve

The practice-administration review found the packet's single most expensive omission and named it twice. In its cross-feat gaps: **"Enrollment ↔ claims has no valve. Readiness produces a verdict; nothing consumes it to *hold* a claim. Between 'we booked anyway' and 'the payer denied it 45 days later' there must be a hold bucket with an owner and a release trigger. This is the single most important seam in the cluster and no doc owns it."** In its missing feats, the same finding priced: **"Retro-Enrollment Claim Hold And Release ... the highest-dollar administrative feature for a small practice and it is absent from every doc in the cluster."**

The same review found the second half of the problem inside the differentiator it most admired. Its verdict on the administrative readiness contract (§2.5) is *differentiator*, and its stated gap is fatal: **"Supervising and substitute arrangements are the glaring hole: an unenrolled NP may be perfectly billable *under* a supervising physician, and a locum may be billable under the absent physician's NPI with the right modifier and within the time limit. A readiness contract that only knows 'enrolled / not enrolled' will produce confidently wrong red verdicts and destroy trust in the feature."** §7.1 repeats it against the readiness graph itself: without substitute arrangements, retroactive effective dates, and mid-year provider changes, **"the graph is confidently wrong in exactly the situations where a small practice most needs it."**

Three more findings from the same review land here rather than anywhere else. §2.2's gap: an approval "routinely carr[ies] retroactive effective dates" and there is no hold-and-release mechanism anywhere. §2.2's failure story, which is the reason the beta practice cares: **"a new provider sees patients for ten weeks before anyone realizes the Aetna application was never submitted."** And the cross-feat gap that no document assembles: **"Provider departure has no owner anywhere"** — affiliation end-dating, payer termination, open referrals, in-flight claims, and records custodianship fragment across five documents, and a mid-year departure is the most common structural event in a small practice.

The frame documents then made this document's shape non-negotiable. FR-3 names the valve this capability *is*, at the top of its list: *enrollment readiness → claim hold with retro-release*, and states the diagnosis in one line — **"a verdict with no valve leaks; a gate with no escape blocks; both are the same omission."** The Thread contract supplies the mechanism and forbids the shortcuts: `held` is a **condition on a station, not a station**, because making it a position "would erase the position they qualify and make retro-release uncomputable"; the shipped hold reasons are a closed list whose first entry is *enrollment not yet effective for this provider × payer × location*; the retro-rebill rendering requires the hold reason to be **"a structured condition — provider × payer × location × date range — never as free text"**; and the enrollment case is one of the eight enumerated non-visit threads, whose **dollars and clock derive from the threads it gates**. Scope-and-sequencing places both capabilities in Slice 3 and calls the first one "the valve that Slice 1's enrollment readiness verdict was computed for."

What is left is what this document decides: what the precondition actually tests, what happens at the deadline, who the claim may be billed under instead, and what a provider's arrival and departure do to all of it.

## Proposed feature set

### 1. Participation is a claim-time precondition, not a status

**Nobody reads a participation status.** The practice asks one question, constantly, in two tenses: *can I book this patient with this insurance on Tuesday* and *may this claim go out*. Both are the same function evaluated at two different moments, and it is stated once here.

The precondition is a **dated tuple**:

| Dimension | Why it is in the tuple | In the beta |
|---|---|---|
| **Billing entity (TIN)** | participation is granted to a group, not to a person in the abstract | constant — one TIN — and stored anyway |
| **Rendering identity** | *not* the person who saw the patient; the identity the claim will name after §5 resolves it | the working dimension |
| **Payer product** | a provider can be in a payer's commercial network and outside its Medicare Advantage or exchange product | stored; tested at payer level in beta, flagged below |
| **Location** | most payers enroll providers per service location, which is why a new suite can be fully built and completely unbillable | constant — one site — and stored anyway |
| **Date of service** | the only dimension that makes retro-release computable | the working dimension |

Two of the five are constant at the beta practice. They are stored regardless, because opening a second suite and adding a second tax ID are precisely the events that break participation, and the review's satellite gap is that a practice discovers this by not being paid.

**The evaluation, stated once:** resolve the billing identity for this encounter (§5, which may substitute it); look up the participation Promise for that entity, identity, payer product, and location; ask whether this date of service falls inside an effective range; return one of five values.

| Verdict | What it means | Booking | Claiming |
|---|---|---|---|
| **effective** | an effective range covers the date | silent | submit |
| **not yet** | an enrollment case is pending; an expected date may or may not be known | warning naming the earliest date and the alternative identity, if one exists | **hold**, release condition = that case's effective date |
| **terminated** | the date falls after a term date | warning | **hold** with no release condition → straight to disposition |
| **never** | the case was denied or withdrawn, or the practice does not bill this payer for this provider | warning: *we do not bill Ambetter for Rosa* | **hold** with no release condition → disposition; the escape may not submit |
| **unknown** | the practice bills this payer, and no case exists for this provider at all | warning plus one click: *start a case* | **hold**, and the hold **mints the enrollment case** |

**Five answers rather than two is the whole point.** "Not yet" is a wait; "never" is a decision; "unknown" is a discovery. Collapsing them into *not enrolled* is what produces a red verdict the office manager cannot act on, and the last row is the one that catches the review's ten-week story — except that a hold catches it at claim time, six weeks too late, which is why §6's runway exists to catch it at hire time and §8's warning to catch it at booking.

**Participation is a declared Promise** (FR-2 retires *effective-dated affiliation*; the screen says *Rosa is not approved with Ambetter yet*). Its maker is the payer, its evidence is the approval letter or roster return, and it carries an effective date and an optional term date. It is never edited: a corrected effective date is a dated reversal and a new assertion, so the claims released under the old one remain explainable.

### 2. The hold: never rejected, never submitted

A claim whose precondition fails does not leave the building and does not disappear. Its thread stays at **`charged`**, and takes the **`held`** condition the Thread contract defines — *"charged, claim HELD,"* exactly as the prototype's close sheet printed it. The position underneath is preserved, which is what makes everything below a query.

The hold record is structured, and no field of it is prose:

| Field | Value |
|---|---|
| reason | `participation-not-effective` (one of the contract's closed list) |
| entity · rendering identity · payer product · location | the resolved tuple from §1 |
| identity that actually rendered | stored separately when §5 substituted it, so the packet can explain itself |
| date of service | the date the release predicate compares against |
| awaiting | the enrollment case thread id, or none |
| release condition | *an effective date on or before this date of service exists for this tuple* |
| owner | a Roster entry — the manager, or Marcy at Ledgerline |
| clock | date of service + the payer's filing window, inherited from the Deadline Control Plane |
| dollars | billed charges (what the Thread contract says the dollar figure means at `charged`) |

**The release condition is a predicate, not a note.** That single choice is what turns retro-release from a hunt through a spreadsheet into one indexed query, and it is the reason the Thread contract forbade free text in the same sentence it named this rendering.

Three properties the hold must have and does:

- **It never blocks the patient.** *Hold the claim, never the patient* is a standing rule of the readiness model; this document contributes exactly zero booking blocks and its severities are only *blocks billing* and *informational*.
- **It is never invisible.** A held thread carries dollars and a clock like any other, so it sorts into Today by dollars × clock and into The Money's "where is our money sitting" rollup as one row. Rosa's seven held claims are *held by* one enrollment case, and that case shows **$2,438.40 and 53 days** because that is the sum and the nearest deadline of what it holds — not because anyone typed a number.
- **It knows why it was booked.** Where the booking warning was overridden, the booked-anyway record — *we knew this and booked anyway, because ___* — rides to the hold, so the person releasing it in eight weeks can read the reason rather than guess it.

### 3. Retro-release, and the remainder nobody may leave behind

An enrollment case reaches `remitted` when the payer answers with an effective date. That transition is the only input this mechanism needs.

**The release, in one action.** Every held thread whose structured condition matches the case's tuple and whose date of service is **on or after** the effective date releases together, at **tier 2 — do + notify**: it executes at once, appears in Today and on the close sheet with a dollar total, and each released thread records one dated transition citing the effective date as the justifying fact. The released claims then enter the ordinary claim batch at **tier 3**, behind the same visible cancel window every outbound batch uses. The manager's screen says *37 claims, $18,204.60, releasing to Ambetter tonight* — one number, one button, one cancel.

**Release is a re-scrub, not a replay.** A claim held for ninety days was built against facts that may have moved: the patient's coverage may have terminated, the subscriber may have changed, the code-set overlay may have rolled. Release therefore rebuilds and re-validates the claim before it goes out, against the freeze rule the Thread contract already states — what a third party will judge us on is frozen (the date of service, the resolved rendering identity, place of service, the price as charged), and everything else is re-resolved from current facts. A claim that fails the scrubber on release **does not release**: it moves to a different hold with its own reason and owner, and is never silently dropped from the batch. The count on the notification therefore has two lines — released, and stopped again — because a batch that quietly shrinks is a batch nobody will trust twice.

**The remainder is the half that gets forgotten, so it is the half with a clearing condition.** Every held thread that matches the tuple but whose date of service falls **before** the effective date appears on one screen with a dollar total and a forced three-way disposition:

| Disposition | What it is | Tier |
|---|---|---|
| **Self-pay** | the balance moves to the patient, entering the FD-4 money path with a written explanation | **3 — delay + cancel**, because it puts a bill in a patient's hands for the practice's own enrollment gap |
| **Appeal or reconsideration** | the payer is asked to extend the effective date or reprocess; the thread stays alive with the appeal's own clock | 2 |
| **Write-off** | the money is gone, with reason `enrollment` in the money taxonomy | **4 — hard stop**, a material adjustment |

**The enrollment case cannot reach `closed` while its remainder list is non-empty.** That is the clearing condition (FR-5), and it is deliberate friction: the alternative is a case marked done with four thousand dollars stranded behind it, which is exactly the state the practice is in today with a spreadsheet.

The write-off reason is a valve, not a label. `enrollment` write-offs feed the **preventable-loss count with dollars** on the owner's monthly one-pager, beside preventable denials. A practice that writes off eleven thousand dollars in a year because applications went in late should see eleven thousand dollars, once a month, next to the name of the thing that caused it.

### 4. The deadline escape, laddered

**A gate that destroys the money it protects is not a control** (FR-6, and the Thread contract repeats it). A held claim's clock is the payer's filing window running from the date of service, and it runs whether or not the enrollment lands. The escape is three rungs and a refusal, and the ladder's order is the design:

| Rung | Fires at | What happens | Tier |
|---|---|---|---|
| **1 — Substitute the identity** | as soon as a lawful arrangement covers the date; re-checked whenever an arrangement is recorded | the claim is re-resolved under a supervising or absent physician who *is* effective (§5), and releases immediately at full value | 2 — do + notify, after the attestation §5 requires |
| **2 — File for the clock** | at the payer's own median days-to-answer before expiry, floored at 14 days | the claim is submitted under the true rendering identity, **expecting a denial**, because a dated denial is proof the claim was filed on time and is the document a later reprocessing request is built on | **3 — delay + cancel window**, with the expected outcome stated in the notification |
| **3 — Decide** | at 7 days, or immediately where rung 2 is refused | the same forced three-way disposition as §3's remainder, with the same reasons | 3 / 4 as in §3 |

**Rung 1 is the only rung that keeps the whole dollar**, which is why substitution is evaluated before the deadline is even close, and why §5 belongs in this document rather than beside it.

**Rung 2 is offered only where the enrollment case is `pending`.** Where the verdict is `terminated`, `never`, or `unknown` with no case opened, submitting produces a denial that protects nothing — there is no future effective date for a reprocessing request to point at — and the practice would be sending a claim it knows cannot be paid for a reason that will not change. In those states the ladder skips to rung 3. This narrows a literal reading of FD-5 and is recorded as a discrepancy below rather than assumed.

**No rung fires silently.** Each is announced on Today with the thread's own clock as the countdown — no second timer, no second mechanism — and the notification names the consequence in one sentence a person can act on: *"Curtis Ames's Aug 25 claim goes to Ambetter on Thursday under Rosa's NPI. We expect it to deny; the denial is what proves we filed in time."*

### 5. Billable under whom: substitute and supervising arrangements

An arrangement is a **declared Promise** whose effect is narrow and total: it changes **which identity §1 tests**. Four are enumerated; a fifth is a contract change, not a configuration.

| Arrangement | The claim names | Modifier | What limits it |
|---|---|---|---|
| **Locum tenens / fee-for-time** | the **absent** physician | **Q6** | a **continuous-day countdown** per absent physician × substitute × continuous period, started by the substitute's first covered service date and running on calendar days |
| **Reciprocal billing** | the **regular** physician | **Q5** | the same continuous-period shape, plus the occasional and informal character of the arrangement |
| **Incident-to** | the **supervising** physician | — | a per-encounter condition: supervision furnished, established patient, established plan of care |
| **Split/shared** | whoever performed the substantive portion | **FS** | a per-encounter attribution, in facility settings the beta practice mostly does not use |

**The day countdown is a clock, therefore it is a Promise with an expiry, therefore it renders.** It is not a field on a person. It appears three ways from one number: the booking surface warns when an appointment for the absent physician's panel falls past the limit; a date of service past the limit **holds with no release condition** and goes to rung 3; and The Practice's fourteen-day negative prints *"Dr. Osei's substitute coverage runs out in 9 days — 4 appointments are booked past it."* The exact limit and the rule that starts a new continuous period are a **shipped rule pack, not an authored setting** (FR-1), seeded from the Medicare shape, varying by payer, and carrying a verification action of the same kind R-1 carries for CPT licensing.

**The locum's two clocks are two clocks.** The Roster names this seam and hands the second one here: a locum's roster end date says when they stop being able to open the software; the Q6 countdown says how much longer their work is billable under the absent physician. They are about the same person and the same absence and they are not the same number, and collapsing them into one field makes both wrong.

**Incident-to is where the product must claim less than it can compute.** openPractice holds the administrative half honestly — the supervising physician exists on the Roster, was scheduled at that location on that date, and is effective with that payer there; the patient is established rather than new, from visit history; the resolved identity passes §1. It does not hold the clinical half and must not infer it. Therefore:

- an **administrative inconsistency is a hold** with a plain sentence — *"billed incident-to under Dr. Vance, who was not at Main St. on Sep 3"* — releasing when the inconsistency is corrected or the arrangement is withdrawn;
- **administrative consistency is not an assertion.** The arrangement requires a **per-encounter attestation from the physician**, captured as a Promise with the physician as maker, delivered as one tap in the *needs the doctor* tray rather than a staff checkbox;
- an encounter marked incident-to with **no attestation** when the claim is built is a hold whose release condition resolves the moment the tap happens — which makes the tray's aging a money number rather than an administrative one.

**Split/shared is modelled, not built.** The type exists so identity resolution has a slot and the modifier has a home; the beta ships a recorded attribution, not a time-and-substance workflow. A practice that rounds in a hospital needs more, and the frame governs surfaces, not schema.

### 6. The onboarding runway

The question a practice asks about a new hire is never *what state is the case in*. It is **"when can I book her insured patients?"** The runway answers it as one date per payer, planned backwards.

```
Rosa's first Ambetter patient
  = the payer's decision date
  − the payer's own historical turnaround        ← learned from this practice's prior cases
  − the application submitted                    ← dated
  − the application prepared and signed          ← the doctor's one tap
  − NPPES correct                                ← several payers pull the address from it
  − CAQH attestation current                     ← one fact, many dependents
```

Every quantity above the line is read rather than typed (FR-8): prior enrollment cases give the turnaround, NPPES gives the identifiers, the CAQH export gives the attestation date. Until this practice has cases of its own, a shipped band stands in and says so — *median 63 days, from published guidance, not from your history.*

**The learned fact that decides everything is whether the payer backdates.** For each payer, `effective date − submission date` across the practice's prior approvals says whether that payer backdates to submission, backdates to the first of a month, or never backdates at all. That single statistic converts the hold from a gamble into a decision: where a payer reliably backdates, booking before the effective date is safe and the hold is a wait; where it never does, booking insured patients before the date is a guaranteed write-off and the warning should say so in those words. It is FD-2's shape — an observed Promise with an n and a spread — applied to a non-money fact, and its cold start is far colder than the allowed-amount baseline's (see Open questions).

The runway emits four things and no report:

- **one date per payer** — *Ambetter Apr 12; Medicare Jun 1; BCBS submitted 41 days ago, this payer's median is 63*;
- **one aggregate** — *Rosa's schedule is fully billable on ___*, which is the only date the physician-owner wants;
- **the negative** — *these 14 booked appointments fall before her earliest date*, with what each is worth, produced the moment the date moves;
- **a standing filter on booking** — the same verdict as §1, evaluated against a future date of service.

**The cascade is one line, not N lines.** A lapsed CAQH attestation stalls every pending application simultaneously; it prints as *"Rosa's CAQH attestation lapsed 9 days ago. 4 pending applications are stalled behind it,"* because four separate red lines with one cause train staff to dismiss all four.

### 7. The departure mirror, and retro-termination

**Participation is terminated with a date. It is never deleted.** That is the entire design, and it is what stops a departure from retro-invalidating a year of correct claims. A term date closes the effective range forward; every date of service inside the old range remains billable, workable, appealable, and correctable after the person has gone.

The mirror's billing half — the Roster owns the access half, and the enrollment atomic owns directory removal and NPPES:

| Item | What must happen |
|---|---|
| **Each participation Promise** | a term date, with the warning that terminating a payer too early kills the tail — the claims for dates of service before the last day |
| **In-flight claims** | keep working. The Roster's offboarding switch reassigns the *owner*; the **rendering identity does not change**, because a corrected claim still names who rendered |
| **Held claims for this provider** | their enrollment will now never land: the release condition is dead and they go to §3's forced disposition **immediately**, rather than aging quietly to a filing deadline nobody is watching |
| **Signatures the practice will need later** | an appeal, a corrected claim, or a records response may need the provider's own signature months after departure. These are backwards-planned from the **last day** the way §6 plans forward from the hire date, and collected before it |
| **Panel and future appointments** | the Roster's mass-reschedule path; named here so departure stops fragmenting across five documents |

**Retro-termination is retro-release read backwards.** A payer that terminates a provider with a retroactive date does not create held claims — it creates **recoupment cases on claims already paid**, using the same tuple and the same date arithmetic in the opposite direction. The computation belongs here; the case belongs to the Recoupment And Overpayment Case File, and the handoff is a named link (*recoups*) rather than a second mechanism. Saying so is what stops someone building a second date engine for the unhappy direction.

### 8. One computation, two valves

The review's top reframing for the whole cluster is one question: **"Is tomorrow's schedule billable?"** This is the mechanism that answers it, and there is only one of it.

**Readiness is derived by dry-running the downstream operation** — build the claim in memory, and if its participation precondition passes, it is claim-ready. The booking warning and the claim hold are therefore *the same function called with a different date*: the warning runs it against a future date of service, the hold runs it against a past one. They cannot drift, because there is nothing to drift from.

| Consumer | When | What it does with the verdict | Severity |
|---|---|---|---|
| **Booking** | live at booking, and nightly for the next 14 days | warns, names the earliest billable date, offers the substitute identity if one exists, and books anyway with a recorded reason | never blocks |
| **Claiming** | when the claim is built | holds, with the structured condition of §2 | blocks billing |
| **The Practice** | weekly | the same function in negative form: every participation fact that will not be true in fourteen days, with a name on each line | informational |
| **The Day** | every morning | tomorrow's threads whose verdict is not `effective`, on the huddle page, with the one-sentence fix beside each | informational |

**Readiness degrades after booking, and that is the common case** — an enrollment terminates, a payer drops the location, a locum's day limit passes between the booking and the visit. The Thread contract already has the move: `ready → booked`, dated, with its reason, so the appointment reappears on the huddle. Anthony Pruitt dropped back for a coverage change; Curtis Ames moved off Rosa's 8:15 to Dr. Osei's 3:15 for this one, and the close sheet could print *"so Ambetter can be billed for him"* because the thread still knew what the morning page had said.

### Rhythm and clearing conditions (FR-5)

| Cadence | What happens | Clears when |
|---|---|---|
| **Day** | the huddle prints tomorrow's unbillable appointments with the alternative identity beside each; a released batch and its dollar total appear on the close sheet | the day closes with a signature |
| **Week** | The Practice shows what will not be true in fourteen days: a term date, an expiring substitute arrangement, a stalled application, a locum day-limit, an unattested incident-to encounter | each line carries a name and a date |
| **Month** | held dollars, released dollars, and `enrollment` write-offs on the owner's one-pager | the write-off total is read, not merely produced |
| **Quarter** | the learned payer turnaround and backdating habit are recomputed from cases closed since the last run | recomputed |
| **Year** | *(not this document's — revalidation and credential expiry belong to the enrollment lifecycle)* | — |

A hold clears when its condition resolves or its disposition is taken — never because someone marked it done. An enrollment case clears when its remainder list is empty. A runway clears when its date passes and the first claim under it is paid.

### Governance tiers (FR-6)

| Action | Tier | Note |
|---|---|---|
| Recording an effective date, a term date, or an arrangement | **1 — do + undo** | it is a dated Promise; undo is a dated reversal, never an edit |
| Placing a hold; a nightly re-evaluation dropping a thread from `ready` | **1** | silent and reversible; the transition carries its reason |
| **Retro-release of a held bucket** | **2 — do + notify** | executes at once, appears in Today and in the close with the dollar total and the stopped-again count |
| Submitting the released batch; the rung-2 deadline escape; converting a held claim to self-pay | **3 — delay + cancel window** | anything leaving the building, including a bill to a patient for the practice's own enrollment gap |
| Writing off a held claim with reason `enrollment` | **4 — hard stop** | a material adjustment, confirmed by a human before the fact |

## Interfaces and boundaries

**Consumes.** Enrollment case transitions and the effective and term dates they carry, from the credentialing lifecycle. Roster references for owners, for the substitute and supervising identities, and for the administrative fact that a supervising physician was scheduled at that location that day. The party and place register for payer, product, and location. The Thread's `charged` station and its `held` condition, unmodified. The timely-filing clock, from the Deadline Control Plane. The claim scrubber, for the dry run and for the re-scrub on release. The practice's own prior enrollment cases (FR-8) for turnaround and backdating, and the CAQH export and NPPES for the runway's upstream steps.

**Emits.** The five-valued participation verdict. The structured hold condition. The release batch and its dollar total. The remainder list with its forced disposition. The resolved billing identity, its modifier, and the date it stops being valid. The first-billable date per payer and the aggregate schedule-is-billable date. **Held dollars and the nearest clock back to the enrollment case**, which is how a gating thread gets a number without anyone typing one. The `enrollment` write-off reason, into the money taxonomy.

**Named valves (FR-3).** Every verdict here names what changes when it changes:

- *participation verdict* → the booking warning (Slice 1) and the claim hold (Slice 3) — one computation, two valves;
- *effective date recorded* → bulk release of every matching held thread, plus the remainder list;
- *remainder list non-empty* → the enrollment case may not close;
- *held claims* → the enrollment case's own dollars and clock;
- *substitute day-limit countdown* → the booking warning, a hold with no release condition, and The Practice's fourteen-day negative;
- *incident-to attestation missing* → a hold; *tapped* → its release;
- *supervising physician not scheduled at that location that day* → a hold naming the inconsistency;
- *term date recorded* → future bookings warn; nothing already billed is invalidated;
- *retroactive term date recorded* → recoupment cases on paid claims, handed off by link;
- *learned backdating habit* → whether the booking warning reads *book it, we'll hold the claim* or *don't book her insured patients yet*;
- *write-off reason `enrollment`* → the preventable-loss count with dollars on the monthly one-pager;
- *filing clock crossing a rung* → the next rung of the escape ladder, with notification.

**The boundary with the enrollment atomic, stated once.** [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md) owns **credential evidence** (licenses, DEA, malpractice, CAQH, PTAN), the **enrollment case lifecycle** (prepare, attest, submit, chase, decide), **directory assertions and attestation**, **revalidation and expiry watch**, and **safe delegation** — including the rule that openPractice never holds an external portal credential. This document owns none of them and must never store a license or contact a payer. It owns the **participation Promise** that a decided case produces, and everything downstream of it: the hold, the release, the remainder, the arrangements, the runway's arithmetic, and the two valves.

The seam runs in both directions and is two lines. **They emit the effective date; I emit the held dollars and the nearest clock.** That is why an enrollment case sorts in the same list as a denied claim, and it is the whole content of the interface.

**The boundary with the Roster, restated in its own words.** *Employment facts gate access; billing facts gate claims.* A provider fully and correctly on the Roster may be entirely unbillable, and that is a **held claim, not an access error**. The two touch at exactly one place — the locum's two clocks — and this document owns the billing one.

**What this document does not own.** The filing-window numbers, corrected-claim and replacement mechanics, and the appeal clock (the deadlines atomic — this document consumes the clock and owns only the escape *decision*). The expected allowed amount (promise memory; participation is a *declared* Promise, the allowable an *observed* one — different assertions about the same payer). The recoupment case (the money atomic). The clinical facts of supervision (the EHR). Access, coverage, offboarding of people, and mass reschedule (the Roster and its neighbours).

**Against the acceptance test.** Nouns touched: the **Promise** (participation, arrangements) and the **Thread** (one hold reason, one condition), referencing the **Roster** and rendering onto the **Day**. Surfaces: Today, The Thread, The Practice — no new one. What it deletes: the participation registry proposed in the payer-contract atomic, the manila-folder spreadsheet of who is approved with whom, and a readiness verdict that nothing consumed. **It introduces no new station, no new surface, and no seventh noun** — one hold reason, one Promise kind, one arrangement enumeration, and two renderings.

## Alternatives and tensions

**Hold versus submit-and-work-the-denial.** What practices actually do is submit everything and work the denials, and it is simpler: no hold, no release, no remainder. It loses for three reasons — a denial for a non-participating provider is not reprocessable at every payer, it consumes the filing window it appears to preserve, and it puts an explanation of benefits in the patient's hands saying *provider not in network*, which is a trust event the practice cannot retract. But the cost of holding is real money sitting still at payers that would have reprocessed freely. The mitigation is §6's learned backdating habit, which turns the argument into data per payer instead of a policy someone has to hold.

**Holding is optimism, and optimism needs a stop-loss.** A hold is a bet that an effective date will land. If it does not, the practice has lost the filing window *and* still owes the patient a conversation. The escape ladder exists to make the stop-loss a date rather than a feeling, and rung 3's forced choice is the only part of this document designed to be uncomfortable.

**The escape's rung 2 narrows FD-5.** The ratified text says a gate that would stall a claim past timely filing auto-releases at tier 3 with notification, without qualification, and the Thread contract repeats it as universal. Refusing to submit where the enrollment case was denied is a narrowing, and it is defensible — a denial that protects nothing is not an escape — but it is a narrowing, and it is recorded below rather than quietly applied.

**The product cannot fully evaluate a compliance surface, and should not pretend to.** Incident-to and split/shared turn on facts about what happened in the room. The position taken here — administrative consistency plus a per-encounter physician attestation — will be argued from both sides: that the product should enforce more (it cannot, without clinical facts it does not hold and should not want) and that it should claim less (which returns the readiness verdict to being confidently wrong, the exact failure the review said would destroy trust in the feature).

**The product dimension is where the wrong-red verdicts will concentrate.** The beta tests four dimensions and stores five; the fifth — payer *product* — is where a provider is in the commercial network and out of the Medicare Advantage or exchange one. Storing it now costs nothing. Discovering it later means re-testing every hold the beta ever placed.

**Two constant dimensions is a trap with a known shape.** One TIN and one location are constant at the beta practice, which makes them easy to omit and expensive to add — and the two events that most reliably break participation are incorporating as a PC and opening a second suite. They are stored from day one for exactly that reason.

**Forcing a disposition will be experienced as friction.** A practice that will not choose between self-pay, appeal, and write-off can simply leave the enrollment case open, which is a softer failure than the spreadsheet but still a failure. Whether the friction is *tolerable* is a beta measurement, not an assertion, and the number to watch is how long a remainder list stays non-empty.

**The learned turnaround has a far colder start than the learned allowable.** Promise memory over remittances reaches a usable n within a month of go-live. Promise memory over enrollment approvals may reach n = 3 in a year at a four-person practice. The shipped band must therefore be honest about being a band, and the runway must say *from published guidance, not from your history* until it is not.

## Open questions

- **Does the beta practice's payer set actually backdate, and what n makes the habit trustworthy?** Everything in §6 that converts the hold from a gamble into a decision rests on this statistic, and it is the packet's thinnest data source. Measure it; do not assert it.
- **What is the continuous-day limit and its restart rule, per payer, for Q5 and Q6?** The Medicare shape is the seed; commercial payers differ and some do not recognize the arrangement at all. This needs the same treatment R-1 gets: a shipped rule pack with a named verification action before the countdown is used to hold a claim.
- **When does a hold become a patient conversation?** A patient whose claim is held for seventy days receives no statement and no explanation of benefits, and then may receive a bill. The current design says nothing to them until §3's self-pay disposition. That silence may be correct and may be the beta's worst patient-experience moment.
- **What happens to an estimate issued before the verdict changed?** A Good Faith Estimate or insured estimate given at booking assumed a billable pair. If participation moves before the claim, the estimate is stale in a way the coverage-change valve already handles for coverage — whether participation belongs in that same invalidation set is unresolved.
- **Who taps the incident-to attestation, and does the physician-owner tap for himself?** The Roster's self-review finding applies: at a two-person practice the attester and the beneficiary of the attestation are the same person. The honest options are to accept it and log it loudly, or to accept that the tray will sometimes be a formality.
- **Is participation per-suite?** The review's satellite gap says a new site can be fully built and completely unbillable. If the design partner opens a second suite mid-beta, the location dimension stops being constant and the first real test of the tuple arrives with no warning.
- **Is there a "call them" clearing action on a held bucket, and who owns it?** At some age a wait should become a phone call to the payer's enrollment department — which is a different phone tree from claims, and Payer Follow-Up By Batch is built for the claims one.
- **Do the runway and the enrollment case duplicate the countdown?** The case has a clock derived from what it holds; the runway has a date derived from the case. If both are stored, they will disagree. The likely answer is that the runway is a rendering, and it should be stated before either is built.
- **Does a Q5, Q6, or FS modifier fragment the learned allowable baseline?** Promise memory keys on payer × code × modifier × place of service. A substitute-billed claim carries a modifier that splits the n for that code, at a practice where n is already small. Real interaction with FD-2, and unresolved.
- **How long should a released-but-stopped-again claim wait before it becomes its own kind of work?** A claim that fails the re-scrub on release has left one hold for another, and nothing yet says whose problem that is on Wednesday.

### Discrepancies recorded against the binding inputs

- **A beta-blocking capability with no slice.** **Provider Onboarding And Departure Runway** is priority **B** in the capability register of [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md) and appears in **no slice** of [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) — the exact defect class whose discovery forced that document's revision. This document proposes it lands in **Slice 3**, beside the hold it feeds; the departure mirror's access half already sits in Slice 0 via the Roster's offboarding switch. Recommended as an amendment, not asserted.
- **Two documents claim participation.** [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md) feat 1 is a *versioned participation registry*. FD-2 demoted that document's contract modelling to an optional arbiter import but did not reassign the participation fact, leaving it owned by a document that has left the critical path. Claimed here: **participation is a declared Promise owned by this document**; expected allowed amounts remain that document's business, now as an observed Promise.
- **The tuple's grain differs across the packet.** The practice-administration review's enrollment-case grain is *provider × entity × location × payer × product × purpose* (six); this document's synthesis brief says *provider × payer × location × date of service* (four). Resolved as **five stored, four tested in beta**, with the product dimension flagged in Alternatives above. *Purpose* is not carried: at one small ambulatory practice it is always "bill for services rendered."
- **A narrowing of FD-5's deadline escape.** FR-6 and the Thread contract both state the escape without qualification. §4 permits the auto-release into submission **only where the enrollment case is pending**, and refuses it where the case was denied, withdrawn, or never opened. Recorded as a proposed narrowing rather than silently applied.
- **A link-text mismatch, not a conflict.** [The Roster](openpractice-small-practice-beta-roster-access-and-offboarding.md) links this document as *"Enrollment-Gated Billing And Retro-Release"*; the register names two separate capabilities that this document jointly owns. The path is the same. Reconcile the link text at the next edit of whichever document loses the argument.
- **Scope statement, for the record.** This document owns **three** register entries — **Enrollment-Gated Claim Hold And Retro-Release**, **Substitute And Supervising Billing Arrangements**, and **Provider Onboarding And Departure Runway** — because the first two are one computation and the third is the same computation planned forward and backward in time. Splitting them would rebuild the tuple resolution three times.

## Relationships

Owns the valve that [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md) FR-3 names first — *enrollment readiness → claim hold with retro-release* — and the three register entries listed above; exercises FR-1 (shipped rule packs and forced dispositions rather than authored policy), FR-2 (*Rosa is not approved with Ambetter yet*, never *effective-dated affiliation*), FR-3 (twelve named valves), FR-4 (the retro-release list with a dollar total, and the preventable-loss count), FR-5 (the day/week/month/quarter rhythm and three clearing conditions), FR-6 (five tiers assigned and a laddered deadline escape), and FR-8 (payer turnaround, the backdating habit, NPPES, and the CAQH export before any authored field).

Binds to [The Visit Thread](openpractice-small-practice-beta-visit-thread.md) without extending it: it supplies one entry on the closed hold-reason list, the **structured** release condition that document's retro-rebill rendering requires, and the held dollars and clock that the **enrollment case** non-visit thread derives its own numbers from. It introduces no station, no condition, and no link type that contract does not already define.

Interfaces with [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md) across one two-line seam — *they emit the effective date; this document emits the held dollars and the nearest clock* — and duplicates none of its credential evidence, case lifecycle, directory, revalidation, or delegation scope. Bounded against [The Roster](openpractice-small-practice-beta-roster-access-and-offboarding.md) by that document's own line: **employment facts gate access, billing facts gate claims**, and the locum's two clocks are the single place the two objects touch.

Consumes the filing clock and hands off corrected and replacement claims to [Secondary, Corrected Claims, And The Deadline Control Plane](openpractice-small-practice-beta-secondary-corrected-claims-and-deadlines.md); hands retro-termination to the **Recoupment And Overpayment Case File**, the `enrollment` write-off reason to the money taxonomy, and the self-pay disposition to the FD-4 money path. Renders into [The Day: Huddle, Close, And The Practice Rhythm](openpractice-small-practice-beta-day-rhythm.md) as tomorrow's unbillable appointments and the released batch on the close sheet.

Sequenced as **Slice 3** in [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md), advancing **D-1** (held claims and stuck threads sort into the day by dollars and clock) and **D-3** (the one-click retro-release list is the provenance chain doing work). Governed by [Framing Decisions](../review/openpractice-framing-decisions.md) FD-5 (the four tiers and the deadline escape this document narrows), FD-3 (a term date and a corrected effective date are dated assertions, never edits), FD-7 (the thread contract this hold rides on), FD-2 (participation is declared where the allowable is observed), FD-4 (the self-pay disposition's destination), and FD-1 (no arrangement authoring, no second approver, no credentialing specialist).

Derives its entire problem statement from [Feat Review: Practice Administration](../review/openpractice-feat-review-practice-administration.md) — the cross-feat gap *"Enrollment ↔ claims has no valve"*, the missing feats **Retro-Enrollment Claim Hold And Release**, **Substitute And Supervising Billing Arrangements**, and **Provider Onboarding And Departure Runway**, the §2.5 and §7.1 finding that a readiness graph without substitute arrangements is *confidently wrong*, the *"provider departure has no owner anywhere"* gap, and the top reframing **"Is tomorrow's schedule billable?"** — as consolidated in [Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md). Absorbs the participation half of [Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md) feat 1, and touches the location and offering facts held by [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md). Modifies no existing document; every amendment it proposes is recorded above rather than made.
