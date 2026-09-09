# Secondary Claims, Corrected Claims, And The Deadline Control Plane — Brainstorm

Status: brainstorm
Kind: report
Summary: the next claim after a payment should write itself from what the thread already froze — the primary claim as submitted joined to the primary remittance as received, service line by service line, with the prior payer's allowed, paid, and every adjustment carried forward against the coverage order in force on the date of service — while crossover is detected before assembly so the practice never duplicates a claim the payer already forwarded and never trusts a forward that silently failed; corrections and withdrawals are governed by the payer's own claim number, so a replacement without one is not a replacement but a duplicate; and every claim and every appeal carries a derived clock from a family of six, seeded from the denials that enforced it rather than from a configuration screen, escalating against the payer's own observed turnaround, released as the deadline escape that every gate — and, more consequentially, every hold — must consult, and terminating in a named state with a dated cause and a dollar figure so that nothing in accounts receivable pretends to be alive.
Topics: openpractice-small-practice-beta, secondary-claims, deadlines
Repository context: openPractice — Tier-1 atomic for the small ambulatory practice beta; Slice 3, "The claim and the promise"
Captured: 2026-08-27

## Possible feats

- **The secondary claim is a query, not a form** — derive the next payer's claim from the posted primary remittance as a rendering of thread history: the prior payer's allowed and paid amounts per service line, every adjustment carried forward with its group, reason, and amount, the patient-responsibility split, and the coverage order in force on the date of service. Nothing is retyped, because nothing needs to be.
- **Crossover detected before assembly, and never trusted permanently** — read the payer's own forwarding indicator, learn each primary→secondary pair's actual behavior, suppress assembly where the pair reliably forwards, and install a *waiting* clock instead of a *filing* clock — one that wakes with time still left to file if the forward silently failed.
- **The payer's own claim number is the license to correct** — replacement and withdrawal carry the number the payer assigned; without it there is no correction, only a duplicate. Capturing that number at acknowledgment is the cheapest, least glamorous, highest-return field in the packet.
- **Correction or appeal, decided by which clock is closing** — five denial buckets, each with a default path, each path governed by a different deadline, with both deadlines shown side by side because choosing wrong burns the window that was still open.
- **One clock per claim, one per appeal, none of them typed** — six named clocks derived from a date the thread already holds plus a Promise, seeded from the practice's own untimely-filing denials rather than from a policy table nobody will maintain, escalating against each payer's own observed days-to-answer.
- **The deadline escape every gate — and every hold — must consult** — one service answering "if I stall this, does money die?", so a tier-3 or tier-4 gate auto-releases before expiry (FD-5); and, because the thing that actually stalls claims in this product is a *hold*, a forced decision surfaces on the last day each remaining option is still available.
- **Terminal states with a cause, so nothing pretends to be alive** — paid, denied-final, withdrawn, written-off, not-pursued, filing-lapsed: each dated, each carrying dollars and a reason, each reaching the close sheet, so accounts receivable finally has a floor.

## Focus

What must the product hold, and for how long, so that the next claim after a
payment writes itself, a correction never becomes a duplicate, the choice between
correcting and appealing is made against the deadline that is actually closing,
and no claim or appeal ever dies of a clock nobody was watching?

## Current state: the seams are where the money is, and none of them had an owner

The revenue-cycle review graded twenty-eight feats and then found that the
cluster's real risk was not missing capability. It was that the money falls
through the **seams between** capabilities, and it enumerated fourteen of them.
Four belong to this document, and all four are the same defect wearing different
clothes:

- **secondary and tertiary claim derivation** — 15–25% of claim volume, "the packet's single best provenance application, and currently named nowhere";
- **corrected claims, voids, and replacement control** with the original payer claim control number — "one of the top three things a biller does after a denial", implied by a parity-floor phrase and owned by no feat;
- **the timely-filing and deadline control plane** — "named as an adopted gap in the claims document but given no owner, no surface, and no interaction with the approval gates that can stall it";
- **no terminal state** — "nothing defines when an item leaves AR, so aging reports grow forever and the 'stuck money' concept has no floor."

The synthesis then escalated the third of these into §6.2, an *unacknowledged
direct conflict*: the constitution requires human authority over consequential
financial action, claim submission is consequential, and a gate that stalls a
claim past its filing deadline destroys the money the gate exists to protect.
FD-5 ratified the resolution in one clause — **every gate carries a deadline
escape** — and then the Thread contract made it structural: *the thread is the
only object that can enforce this, because the thread is the only object that
holds the clock.*

This document is the other half of that sentence. The Thread holds *one* clock —
the nearest deadline that costs money — and displays time remaining. It does not
compute the family of deadlines that clock is selected from, it does not know
which of them a gate is about to cross, and it does not classify what happens
when one reaches zero. That is what a control plane is for.

**The reframe this document inherits** is the review's first: from "revenue cycle
management" to **money in motion, with a stuck detector**. Under it, every
capability below is either a station transition or a stuck-item worklist, and a
claim cannot sit between stations without being visible. The two reframes that
follow it govern the shape: **provenance as generator** rather than audit trail
(FD-6 — the secondary claim is the generated artifact that most obviously repays
the chain), and **denial management as the practice's rejection memory** (every
worked denial promotes a rule, a default, or an upstream fix, and the scrubber is
scored by recurrence rather than by rule count).

## Proposed feature set

### 1. Secondary derivation as a rendering of thread history

The Thread already freezes exactly two of the four documents this needs, and it
freezes them for a stated reason: **it freezes what a third party will judge us
on.** The claim exactly as submitted and the remittance line exactly as received
are copies rather than references, because the appeal and the audit are arguments
about those documents. The secondary claim is those two copies joined and
re-emitted.

| The secondary needs, per service line | Where it already is |
|---|---|
| What we billed, and the code, modifier, place of service, units, and dates as sent | The frozen claim as submitted |
| What the prior payer allowed, and what it paid | The frozen remittance line as received |
| Every adjustment the prior payer made — its group, its reason, its amount | The frozen remittance line as received |
| What the prior payer left to the patient, split by kind | The frozen remittance line as received |
| The prior payer's adjudication date and its own claim number | The acknowledgment and the remittance |
| Who is next, and on what authority | The coverage order in force **on the date of service** — a Promise, not today's belief |

That table is the whole feature. **The derivation is a read, not a build**, which
is why the field list on the Thread and this artifact land in the same change or
neither does (FR-4). The Thread's own rendering table already names it and already
names what it therefore must carry: *the posted primary's allowed, paid, adjustment
codes, and patient responsibility; the coverage order in force on the date of
service.* Nothing here extends that list. This document is the consumer that
justifies it.

*Concretely.* Marguerite Bell's Tuesday visit bills $138.00. Medicare allows
$103.40, pays $82.72, and leaves $20.68 to the patient as coinsurance. Her
supplemental plan owes that $20.68, and the claim that asks for it must state
what Medicare did — allowed, paid, and each adjustment with its amount — or it
will be rejected for missing prior-payer information. Every number in the
preceding sentence is already on her thread, frozen, at the moment the
remittance posted. The biller's work is to look at it and press one key.

**Tertiary is the same rendering run twice**, with one added rule: the second
derivation carries *both* prior payers' adjudications, and the coverage order is
read once, from the date of service, rather than re-derived at each hop. Coverage
order is a Promise with an effective range; a patient whose plans switched in
March does not retroactively change what was true in February.

**What the page says.** Not "CAS segments" and not "other-payer adjudication."
The screen says *"BCBS owes $20.68. Medicare allowed $103.40 and paid $82.72 on
Aug 26. Send it?"*

**Governance: tier 1 to derive, tier 3 to send.** Deriving and queuing is
reversible and silent. Anything leaving the building runs a visible countdown
(FR-6). At 15–25% of claim volume a countdown becomes wallpaper, so the queue is
split the way clean auto-posting is split: **a derivation is *clean* when
crossover is ruled out, the coverage order is unambiguous on the date of service,
every adjustment carries forward without residual, and patient responsibility is
fully explained.** Clean derivations batch and send on the countdown. Everything
else queues with the specific missing fact named.

### 2. Crossover detection, and the failure it hides

Medicare forwards a large share of its claims to the supplemental or secondary
payer automatically. Filing our own on top of it manufactures a duplicate denial
— a denial class the practice creates for itself, worked at full cost, that never
had a dollar behind it.

The naive fix is to read the payer's forwarding indicator on the remittance and
suppress assembly. That is correct and insufficient, because **the dangerous case
is not the crossover that happened; it is the crossover that was supposed to
happen and did not.** A suppressed claim with no clock is a claim that lapses in
silence, and the practice discovers it at day 400 with nothing to do about it.

So crossover produces a **different clock, not the absence of one**:

- **Suppress assembly** when the remittance says the claim was forwarded, or when the primary→secondary pair has forwarded reliably in the practice's own history (FR-8: the pair's behavior is learned from observed secondary payments arriving with no claim of ours behind them, not configured).
- **Install a waiting clock** in place of the filing clock. Its deadline is the earlier of (the pair's observed days-to-secondary-payment, with margin) and (the point at which half the secondary's own filing window remains).
- **Wake and derive** if the secondary payment has not landed by then, with enough window left to file normally. The thread moves from waiting to filing without anyone noticing that it nearly did not.

That third rule is the entire justification for the second. A suppression that
cannot expire is a leak with a policy attached.

*What the page says:* "Medicare sent this to BCBS on Aug 26. We are waiting, not
filing. If BCBS has not paid by Sep 30 we will file it ourselves — 61 days will
still be left."

### 3. Corrections and withdrawals: the payer's number is the license

A corrected claim replaces one the payer already has on file. A withdrawal
removes one that should never have existed. Both are transmitted as a claim that
*points at* the prior claim by the number **the payer** assigned to it — not the
number the clearinghouse assigned, and not ours.

This makes one unglamorous field load-bearing across the whole document, exactly
as the review found: *capture the payer's claim control number at acknowledgment
and carry it everywhere.* It is the key that makes every subsequent interaction —
the phone call, the appeal, the correction, the withdrawal — possible at all.

**Therefore the hard rule: no payer claim number, no replacement.** A "correction"
sent without one is not a correction; it is a second original, and the payer will
deny it as a duplicate, consume a chunk of the filing window, and leave the
practice worse off than before it tried. Where the number is missing, the product
does not offer the correction path — it offers the two paths that remain (call to
obtain the number, or appeal) and says why.

| Action | What it means | Governance tier | Notes |
|---|---|---|---|
| **Replace** | The claim was ours to fix: wrong modifier, wrong date of service, wrong rendering provider, missing referring provider, a corrected diagnosis arriving from the EHR | 3 — delay + cancel window | Requires the payer's claim number. Emits a **diff sheet**: what changed, what did not |
| **Withdraw** | The claim should not exist: wrong patient, wrong entity, a true duplicate, a service not rendered | 3, or **4 when the claim has already been paid** | A withdrawal of a paid claim is money leaving the practice through a different door, and it usually produces a takeback |
| **Appeal** | The claim was correct and the payer's decision is what we dispute | 3 — delay + cancel window | Emits the **appeal packet**; applies `disputed`, which suppresses the patient's statement |

**Duplicate protection is structural, not a warning.** The Thread already
publishes a key set for re-association — payer, member id, date of service,
rendering provider, billed amount, and the payer claim number once known. A new
*original* whose key set matches a thread already at `claimed` or `remitted` is
refused, not warned about, because a warned duplicate at a four-person practice
is a duplicate. The legal move is a replacement, and the product says so.

**A corrected claim runs the loop forward, never backward.** The Thread contract
is explicit: a corrected date of service, diagnosis, or provider arriving from the
EHR after the claim went out does not rewind the thread. It enters as a dated
event citing the EHR as its source and drives `claimed → claimed` (replacement)
or `remitted → claimed` (corrected claim). The appeal packet then shows both the
fact as originally submitted **and** the correction — which is the only honest
rendering, and the reason the diff sheet exists as an artifact rather than as a
log line.

### 4. When correction beats appeal

Choosing wrong burns the window that was still open, and the two windows are
different lengths measured from different dates. This is the one screen in the
product that must show **two** clocks, and the exception is argued for in
*Alternatives and tensions* rather than smuggled.

The five operational denial buckets, each with a default and a governing clock:

| The denial says | Default path | The clock that governs | What it costs to choose wrong |
|---|---|---|---|
| Coverage, member, or COB is wrong | Re-verify, then **replace** | Remaining filing window from the date of service | An appeal on a coverage defect is refused on its merits and the filing window closes underneath it |
| Coding, bundling, or a modifier is wrong | **Replace** | Remaining filing window; a replacement filed after it expires is denied as untimely | Appealing our own coding error spends 30–45 days to arrive back at the same fix with less window |
| Authorization or medical necessity | **Appeal** with evidence | Appeal window from the remittance date | A replacement resubmits the same disagreement and earns the same denial, and the appeal window ran the whole time |
| Patient responsibility | Move to the statement | The statement cycle, and any `disputed` suppression | Appealing a correct adjudication delays the patient's own bill |
| Not our money | **Write off**, with a reason | None — this is a terminal state | Leaving it open is how an aging report loses its floor |

Two windows sit underneath every row and both are displayed: the **remaining
filing window** (from the date of service, and it is what a replacement spends)
and the **appeal window** (from the remittance date, and it is what an appeal
spends). Where a contract sets a reconsideration window instead — commonly 90–180
days from the remit, contract-set rather than payer-policy-set — that is a third
candidate and the nearest of the three governs the recommendation.

**The appeal packet is a one-click rendering, not a workflow.** The Thread already
carries everything it needs and says why: the eligibility answer as received with
its timestamp; the authorization number, units, and expiry; the charge as coded;
the claim exactly as submitted; the acknowledgment; the remittance line with its
codes; and the reference to the clinical fact and who signed it. Assembly is the
artifact FD-6 exists to name — an hour of hunting becomes a click, roughly twice a
month at this practice size, and the letter template plus the payer's claim number
plus the remittance image resolves most first-level administrative appeals without
touching the chart at all.

### 5. The deadline control plane

**Six clocks. Every one derived; none typed.** A clock is a date the Thread
already holds plus a Promise. The Thread displays the nearest one and names it in
plain language; the control plane holds all of them and answers questions about
the others.

| Clock | Runs from | Against which Promise | Consumed by |
|---|---|---|---|
| **Primary filing** | Date of service | The payer's filing window | The claim's own release; the huddle page's expiring list |
| **Secondary filing** | The **primary's adjudication date**, not the date of service | The secondary payer's COB filing window, frequently shorter | The secondary derivation queue; the crossover wake rule |
| **Crossover wait** | The primary's forwarding date | The pair's observed days-to-secondary-payment | The suppression that must expire (§2) |
| **Appeal** | The remittance date, per level | The payer's appeal window for that level | The correction-versus-appeal recommendation |
| **Reconsideration** | The remittance date | The **contract's** reconsideration window, where a contract is loaded as arbiter | The same recommendation, as a third candidate |
| **Obligation** | The letter's own date | The demand's decide-by date, the records request's due date, the refund's statutory clock | The recoupment, records-request, and credit-balance threads, which borrow their dollars from what they gate |

**Where the windows come from, and why there is no configuration screen.** FR-8
requires every setup field to name the artifact it could be read from. For filing
windows the artifact is unusually good: **the practice's own untimely-filing
denials.** A payer telling us we missed its deadline is that payer enforcing its
own window against us in writing, dated, on our own thread. It is the most
authoritative statement of the window the practice will ever hold, and it arrives
free.

The seeding order is therefore: the statutory windows that are public (Medicare's
twelve months from the date of service); then the payer's own remittance and EOB
text where it states one; then a **conservative shipped default** — the shortest
window commonly seen — so that the first year errs toward filing early; and then,
progressively, the window the practice's own denials have proven. A claim denied
for untimely filing is a loss *and* a calibration, which is the only consolation
available and worth taking.

**Escalation is learned, not authored.** FR-2 retires "escalation ladder" from
every surface and FR-5 requires a clearing condition, so the mechanism is one
line: **a claim escalates onto the day when its remaining window drops below twice
the payer's own observed median days-to-answer** (FD-2's promise memory, which the
product already computes). Ambetter answering in a typical 21 days means an
Ambetter claim escalates at 42 days remaining — early enough that a refile still
has room, late enough that it is not noise. Nobody sets a threshold; the payer's
own behavior sets it, and it updates itself as the payer changes.

**The clock is not optional on a claim leg.** The Thread contract allows an empty
clock where no deadline exists, and that allowance is right for a waitlist entry
or an unmatched page. It should be read as *unavailable to a claim*: from
`charged` forward, a date of service plus a payer always yields a filing window,
so a claim thread with no clock is a defect rather than a state.

### 6. The deadline escape — and the harder case, which is a hold

FD-5's rule reads: *a gate that would stall a claim past timely filing
auto-releases at tier 3 with notification, because the gate otherwise destroys
what it protects.* Implemented, that is one service with one question:

> **Before any tier-3 or tier-4 gate begins its countdown or its wait, it asks the
> control plane: if I hold this to my own expiry, does any clock on this thread
> cross zero? If yes, I do not hold it — I release at tier 3 with notification and
> say why.**

The gate does not reason about deadlines and does not need to know which clock
was closest. It asks; it is told; the release is a dated transition with the
reason attached, so the close sheet can print it.

**But the thing that actually stalls claims in this product is not a gate.** It
is a **hold** — and holds are not governance tiers. The enrollment-gated claim
hold exists precisely to stall claims, deliberately, for as long as an enrollment
takes, and the Thread contract ends a hold exactly one way: *automatically, when
the condition resolves.* Rosa's seven Ambetter claims are the fixture: $2,438.40,
oldest 37 days, and 53 days of clock remaining because Ambetter's window is 90
days from the date of service. The enrollment case is `claimed` and the payer has
not answered. If it answers on day 91, the hold worked perfectly and the money is
gone.

**A hold therefore needs an escape, and the escape cannot be "submit anyway."**
An enrollment-invalid claim will deny; submitting it blindly converts a held claim
into a denied claim and consumes the window. The honest escape is a **forced
decision on the last day each option is still available**, with the options priced:

| Option | Its own deadline | What it costs |
|---|---|---|
| **Submit and let it deny, then appeal on the backdated effective date** | Needs the filing window *plus* the appeal window it will create | Preserves the payer's dated record and the appeal right; spends 30–45 days |
| **Bill the patient** | Needs whatever notice the practice's own policy requires | Collectible, but wrong if the enrollment backdates a week later, and a refund follows |
| **Write it off** | None; it is terminal | Honest, final, and the number the owner should see before it happens rather than after |

The control plane surfaces this as **one decision with three dated options and a
dollar figure**, at the last date on which all three are still open — not on the
last day, when only the third remains. That is the difference between a control
and a post-mortem, and it is the single hardest thing in this document to get
right.

**Where the escape is refused.** Tier 4 exists for money leaving the practice,
material adjustments, and record merges. None of those is a claim submission, so
in practice the escape releases tier-3 gates and forces tier-4 *decisions* rather
than bypassing them. A deadline never authorizes an unapproved refund; it
authorizes the practice to be told, in time, that one is now the only option left.

### 7. Terminal states, so nothing pretends to be alive

The review's fourteenth seam is the one that quietly poisons every number in the
product: *nothing defines when an item leaves AR, so aging reports grow forever
and the "stuck money" concept has no floor.* The Thread has exactly one terminal
station, `closed`, defined as *nobody owes anybody anything*. That is correct and
underspecified: six very different endings satisfy it, and only one of them is
good news.

**Closing therefore requires a reason from a closed list, and the reason is what
gets rendered** — never the station.

| Ending | What it means | Set by | Tier | What it emits |
|---|---|---|---|---|
| **Paid** | The balance resolved to zero with money | The remittance and the bank line | 2 | Nothing; this is the normal path |
| **Denied-final** | Appeal levels exhausted or declined, with the payer's last reason | A person, from the denial work | 4 above threshold, 2 below | A line on the monthly recurrence report |
| **Withdrawn** | We removed the claim; there is nothing to collect | A person | 3, or 4 if it was paid | The takeback it will probably cause |
| **Written-off** | We are choosing not to collect, with a reason from the money taxonomy | A person | 4 above threshold, 2 below | The journal entry, at its named account |
| **Not-pursued** | A recorded, deliberate decision that the amount was not worth the touch | A person, or a shipped small-balance threshold | 2 | An aggregate, not a line — it is negotiation evidence |
| **Filing-lapsed** | A clock reached zero and nothing went out | **Nobody. Derived, and never typed.** | 2 — do + notify | A dated loss with a **cause**, and a dollar figure |

Two of those rows carry the weight.

**`filing-lapsed` is derived, exactly as `stuck` is derived.** If a person could
type it, it would be typed to clear red, and within a month the practice's own
loss number would be a lie. The clock reaching zero is what sets it, and the
transition names the cause it inherited — *held for enrollment 61 days*, *waiting
on a signature*, *nobody worked the denial* — because the cause is what the fix
attaches to. It appears on the day's close sheet and it aggregates into the
monthly **preventable-loss count with dollars**, which the review named as the
single number that proves the whole thesis to the practice and to the market.

**`not-pursued` must never be conflated with `filing-lapsed`.** They have the same
financial outcome and opposite meanings: one is a decision, the other is a
failure. A product that reports them together will be caught doing it once and
will not be believed again.

### 8. Rejection memory

The review's third reframe: *from denial management to the practice's rejection
memory.* Every rejection, denial, and override worked once must produce a rule, a
default, or an upstream data fix that prevents its recurrence — and the scrubber
is **scored by recurrence rate, not by rule count**. Success is a shrinking
workbench, which is the inverse of how denial products are sold, because their
economics reward denial volume rather than denial extinction.

Three promotion paths, and the product must offer whichever one actually fixes it:

| What the denial reveals | Promotes to | Example |
|---|---|---|
| The claim was assembled wrong in a way a check could catch | A **scrubber rule**, as a row rather than a release | "This payer wants a referring provider on this code" |
| A fact in the party register is wrong | An **upstream fix**, applied once | The member id transposed at registration; a payer id normalized. Fixing the record once retires the recurring failure |
| Our belief about the payer is wrong | A **Promise correction** | The filing window is 90 days, not 180 — learned from the denial that enforced it |

**Every rule scores itself, or it becomes superstition.** FR-3 already names the
valve: *override with a reason → its eventual adjudication outcome, so the rule
can score itself.* Attach every override to what the payer eventually did, and the
monthly report writes itself: *this rule fired 40 times; the overrides paid 38 of
40.* A rule whose overrides pay is **deleted, not tuned** — that asymmetry is what
keeps the rule set from accumulating into the thing it was built to replace. The
mirror report is equally cheap: *authorization gates were overridden 14 times;
9 paid, 5 denied for $3,100.*

At community scale this is the differentiator no proprietary vendor can copy
without exposing its own book: an opt-in, anonymized shared rule pack. That is
post-beta, and it is only credible if the single-practice version already works.

### The artifacts, and the minutes (FR-4)

Every provenance link below names what it emits and what it saves. A link that
cannot fill the third column stores less.

| Artifact | Who touches it, when | What it replaces |
|---|---|---|
| **The auto-derived secondary claim** | The biller, minutes after posting a primary | 6–10 minutes of retyping per claim across 15–25% of claim volume — the largest single recurring keystroke cost in the cluster |
| **The appeal packet** | The biller, on a denial worth fighting | An hour of assembly from four systems, twice a month |
| **The corrected-claim diff sheet** | The biller, before a replacement goes; the payer, on the phone | The duplicate-denial round trip: 30–45 days and a manufactured denial |
| **The expiring-money list** | The manager, on the huddle page and weekly on The Practice | The timely-filing sweep nobody runs, replaced by a list with dollars and a name on each line |
| **The lapsed-money record** | The manager at the close; the owner monthly | Nothing — this is new information, and it is the preventable-loss count with dollars |
| **The recurrence report** | The owner, monthly | The question "why does this keep happening", answered with a cause instead of a count |

### Rhythm and clearing conditions (FR-5)

| Cadence | What happens | Clears when |
|---|---|---|
| **Day** | The huddle page shows what dies this week, by dollars and clock; the close sheet gains one line — *nothing lapsed today*, or what did and why | A submission resets the clock, or a terminal state is recorded. Never by a person marking it done |
| **Week** | Primaries posted Monday derive secondaries that go Tuesday; stalled claims group by payer so one twenty-minute call clears twelve threads | The secondary reaches `claimed`, or crossover is confirmed and a waiting clock replaces the filing clock |
| **Month** | The recurrence report and the preventable-loss count with dollars | It is printed; it is a report, and it is labelled one |
| **Quarter** | Filing and appeal windows re-derived against the quarter's denials — did any payer's window change? | The Promises are updated with their new evidence |
| **Year** | Appeal levels and windows refreshed at contract renewal, alongside the arbiter import for the one payer the practice most distrusts | The renewal conversation happens |

### Naming (FR-2)

**"Control plane" is an internal name and does not ship**, exactly as "Thread"
does not. Staff name the subject and the station, and the retired vocabulary here
is unusually dense:

| Never on a surface | What the page says |
|---|---|
| timely filing / filing window | 16 days to refile this or we lose it |
| crossover indicator | Medicare already sent this to BCBS on Aug 26 |
| frequency code 7 / 8 | Replace the claim we sent Aug 12 · Withdraw it |
| original payer claim control number | Aetna's own number for this claim |
| CAS segments / other-payer adjudication | What Medicare did with each line |
| terminal state / AR floor | Finished, and why |
| deadline escape / gate release | We sent it without waiting, because we had 9 days left |
| appeal level 2 | BCBS said no again; there is one more step and 42 days to take it |

## Interfaces and boundaries

**Consumes.** The Thread's five properties and its frozen documents — the claim
exactly as submitted, the remittance line exactly as received — plus its
transitions, its key set, and its links. Promises for every clock: the payer's
filing window, the appeal window per level, the contract's reconsideration window
where an arbiter is loaded, the coverage order effective on the date of service,
and FD-2's observed median days-to-answer that sets escalation. The party register
for subscriber and payer facts, by reference. The Roster for owners. The
clearinghouse connector for acknowledgments, the payer's claim number, and the
mapping of *replace* and *withdraw* onto the wire format.

**Emits.** Transitions — `remitted → claimed` and `claimed → claimed`, each with
a **named type**. The clocks themselves, as a service that gates and holds
consult. Terminal classifications with causes and dollars. The six artifacts.
Promoted scrubber rules, upstream party-register fixes, and Promise corrections.
An *expectation* of money on a secondary, which the Money Line then owns.

**Boundaries, stated so they become contract tests.**

- **Enrollment owns the hold; this document owns the clock that makes the hold expire** and the forced decision when it does. The hold's release condition is structured, provider × payer × location × date range, and retro-release remains a query the enrollment capability runs. This document never releases a hold on its own authority; it forces a dated human decision when doing nothing has become a choice.
- **Money Lines own where money lands.** A derived secondary emits an expectation, not a receipt. The takeback a withdrawal causes, the offset a recoupment applies, and the payer-level adjustment that makes a wire smaller than the claims listed on it are all Money Line business — and the seam between them and this document is exactly the one the revenue-integrity review named sixth: *a recoupment against a two-year-old claim can arrive after the appeal window has closed, and nothing connects them.* The connection is this: **a recoupment thread borrows an appeal clock from the claim it recoups**, computed at the moment the demand letter lands, and if that clock is already at zero the letter says so before anyone spends an afternoon on it.
- **The connector owns transport and the wire format; this document owns meaning.** Frequency codes, loop structures, and segment names live behind the clearinghouse connector, which is also what keeps X12 implementation guides out of the repository (FR-7). CARC and RARC are license-free and ship in the core; they are **read, never authored**, and never rendered as a primary label.
- **The EHR owns the clinical fact.** A corrected diagnosis or date of service arrives as a dated event citing the EHR and drives a replacement forward. It never rewinds the thread, and the appeal packet shows both versions.
- **Reporting owns metric semantics.** The preventable-loss count and the recurrence rate are defined here as behaviors and published there as frozen formulas with visible denominators.

## Alternatives and tensions

**Derive the secondary here, or let the clearinghouse do it.** Most clearinghouses
will generate a secondary from an 835 for a per-claim fee, and buying it is
cheaper than building it. It also moves the product's single most persuasive
provenance artifact outside the product, at the one place where the chain most
visibly repays its cost. The proposal is to derive locally and, where the
clearinghouse also offers it, treat theirs as a **check rather than a substitute**
— a disagreement between the two derivations is a bug in one of them and worth
finding early.

**Two clocks on one screen, against the Thread's one-clock rule.** The Thread
displays the nearest deadline because a page with four countdowns per row is a
page nobody reads, and that judgment is right. But the correction-versus-appeal
decision *is a comparison between two clocks*, and showing one of them makes the
recommendation unauditable by the person taking it. This document asks for exactly
one exception, on exactly one surface, and accepts that the runner-up stays
retrievable-not-displayed everywhere else.

**Auto-send clean derivations, or review every one.** Reviewing all of them is
safe and will not happen; a countdown on a fifth of claim volume becomes wallpaper
within a month. Auto-sending narrows the risk to the definition of *clean*, which
is the same bet remittance auto-posting already makes and the same mitigation
applies — define clean narrowly, make reversal trivial, and put the day's sends on
the close sheet.

**Learned filing windows have an unpleasant calibration source.** The most
authoritative evidence of a payer's window is a denial that enforced it, which
means the product learns by losing. The mitigation is a conservative shipped
default and public statutory windows first, so that the first year files early and
the learning is mostly confirmation. The alternative — a maintained policy table —
is a configuration surface at a four-person practice, which is the failure mode
FR-1 exists to delete.

**Terminal honesty is unpopular by construction.** A close sheet that prints
*filing-lapsed, $412, held for enrollment 61 days* is a public record of a
mistake, and the pressure to soften it into an ordinary `closed` will be real and
will come from a sympathetic direction. The counter-argument is the review's:
an aging report with no floor is not kinder, it is merely quieter, and the causes
are almost always structural rather than personal.

**Rejection memory can ossify into superstition.** A rules engine with no outcome
feedback accumulates rules that were true once about a payer that has since
changed. The counter-rule — a rule whose overrides pay is deleted rather than
tuned — is easy to state and hard to keep, because deleting a rule feels like
removing a control.

**Confident wrong defaults on correction-versus-appeal.** A default that says
*replace* on a bundling denial trains a practice to spend its filing window
arriving back at the same answer. The defaults above are drawn from the review's
five buckets and are more likely right than a coin flip, but they must be scored
the same way scrubber rules are — *this default was taken 22 times; 17 resolved* —
or they are opinions in a table.

## Open questions

**Discrepancies between binding inputs, recorded rather than resolved:**

- **`dollars × clock` or `dollars × age`?** [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md) describes Today as showing *"what is stuck by dollars × clock"*; [The Visit Thread](openpractice-small-practice-beta-visit-thread.md) describes the same block as *"what is stuck by dollars × age."* Both are binding and they differ, and this document's entire output is the clock half. The working proposal — sort by clock where a clock exists and by age where it does not — is a resolution, not a ratification, and the two documents should agree in one place.
- **One legal move, three meanings.** The Thread's transition table maps the secondary, the corrected claim, and the appeal all onto `remitted → claimed`. They install different clocks, emit different artifacts, and carry different governance. Naming the transition type is what saves it — and whether a required transition type is *additive* (a minor bump, per the contract's own list) or needs `visit-thread/1.1` is a real versioning question with a stated budget of at most one breaking change before beta exit.
- **`closed` needs a required reason.** §7 makes closure carry one of six causes from a closed list. The Thread contract defines `closed` without one. Same versioning question, same answer needed before the first consumer reads it.
- **The escape is written for gates; the stall comes from holds.** FD-5 and FR-6 both say *gate*. The enrollment hold is not a gate, is not a tier, and is the mechanism most likely to push a real claim past filing in the beta. Either the rule extends to holds explicitly, or §6's forced decision needs its own ratification.
- **Slice 3 derives secondaries; Slice 4 ingests paper remittances.** [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) places **Manual EOB And Paper-Check Intake** in Slice 4 and **Secondary And Tertiary Claim Derivation** in Slice 3. A secondary derived from a *paper* primary needs the paper primary's fields to exist. Does Slice 3 ship 835-only derivation and accept that paper primaries wait, or does a minimal paper-EOB capture move forward? The fixture's Humana paper check of $743.28 says this is not hypothetical.

**Design questions this document does not settle:**

- Does the beta model **appeal levels beyond the first** — each with its own forms, clocks, and evidence requirements — or classify at level 1 and hand the rest to the payer's portal with a clock attached?
- Who owns the **secondary's expected allowed amount**? Promise memory for a secondary payer is thin (low n by construction), and the alternative — the residual after the primary — is arithmetic that is right until a secondary applies its own fee schedule.
- What is the **conservative shipped filing default** before any untimely-filing denial has been seen, and does it differ by payer class the way eligibility staleness windows do?
- Where a payer forwards to a secondary **that then denies**, does the practice inherit a claim it never filed — with an appeal right, a clock, and no submission of its own to correct?
- Does a **withdrawal of a paid claim** always require tier 4, or only above the same threshold that governs material adjustments? The takeback it causes is money leaving the practice, but it leaves by the payer's hand rather than ours.
- **Retention against append-only history (R-6):** the frozen claim-as-submitted for a thread that lapsed is evidence of a loss. How long is it kept, and does a required deletion reach it? Named here because this document creates more frozen documents than any other.
- Does the practice ever get to see, and act on, **the aggregate of `not-pursued`**? It is the cheapest negotiation fact in the product and the easiest one to leave uncounted.

## Relationships

Bound by [The Visit Thread](openpractice-small-practice-beta-visit-thread.md),
which is authoritative for every thread mechanic used here — the five properties,
the ten stations and their legal moves, the three conditions, the frozen
documents, the key set, and the renderings table that already names the
secondary-claim autofill and the appeal packet as consumers this document exists
to be. Governed by
[Feature Normalization](openpractice-small-practice-beta-feature-normalization.md)
for FR-1 through FR-8 and for the capability names used above — **Secondary And
Tertiary Claim Derivation**, **Corrected Claims, Voids, And Replacement Control**,
**Timely-Filing And Deadline Control Plane**, **Appeal Packet**, **Payer
Records-Request Tracker**, and **Payer Follow-Up By Batch**. Sequenced into Slice 3
by [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md),
whose exit criteria include one live ERA posted end-to-end.

Pairs with the two Tier-1 atomics on either side of it:
[Enrollment-Gated Billing](openpractice-small-practice-beta-enrollment-gated-billing.md),
which owns the hold whose clock §6 must escape and whose retro-release this
document's terminal states must not pre-empt; and
[Money Lines And The Bank Inbox](openpractice-small-practice-beta-money-lines-and-bank-inbox.md),
which owns where a derived secondary's money lands, the takeback a withdrawal
causes, and the payer-level adjustment that makes a wire smaller than the claims
listed on it.

Supersedes nothing and modifies nothing. It supplies the owner for four
capabilities that
[Claims And Remittance](openpractice-feature-list-claims-and-remittance.md)
described as a parity-floor phrase — secondary and corrected claims, and the
"native timely-filing control plane" that document names as an OpenEMR gap without
claiming it. Adjacent research:
[Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md)
for the coordination-of-benefits order this derivation reads, and
[Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md)
for the arbiter import that supplies the contractual reconsideration window.

Derives its substance from
[Feat Review: Revenue Cycle](../review/openpractice-feat-review-revenue-cycle.md)
— CA-4's finding that secondary assembly is "literally a provenance query rendered
as X12", CL-2's payer-claim-control-number finding, CL-5's five denial buckets and
its correction-versus-appeal gap, the twelve missing feats, the fourteen cross-feat
seams, and all three top-level reframings. The gate-versus-deadline conflict is
[Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md) §6.2; the
takeback-versus-deadline seam and the contractual reconsideration window are
[Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md).
Governed by [Framing Decisions](../review/openpractice-framing-decisions.md) FD-5
(the deadline escape from every gate), FD-6 (provenance as artifacts), FD-2
(promise memory as the source of the escalation threshold), FD-3 (corrections as
dated reversals, never edits), FD-1 (the 2–4 staff frame and the external biller as
a Roster entry), and FD-11 (X12 abstracted behind the connector). The money page
and close-sheet figures used as fixtures throughout are from
[Paper Prototypes](../review/openpractice-paper-prototypes.md).
