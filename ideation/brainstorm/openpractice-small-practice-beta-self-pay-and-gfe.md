# Self-Pay, Time-Of-Service Collection, And Good Faith Estimates — Brainstorm

Status: brainstorm
Kind: report
Summary: the money path for a patient with no payer in it — one price list resolved by date of service, one self-pay discount stated once as a dated Promise with the owner as its maker, a strict split between the insured quote (a spoken range that is a rendering of promise memory and creates no obligation) and the written Good Faith Estimate (a Declared Promise with a maker, a delivery record, a six-year retention, and a $400 variance threshold), a dispute that rides the Thread as an enumerated non-visit case whose `disputed` condition suppresses statements on the visit it references, collection at the desk by card, cash, or a texted link that needs no account, one receipt and one allocation at capture, a reason and a destination account on every dollar including the courtesy discount that is not a write-off, the drawer counted into the day's close, and a defined answer in both directions when coverage turns up after the visit.
Topics: openpractice-small-practice-beta, self-pay, gfe
Repository context: openPractice — Tier-1 atomic for the small ambulatory practice beta; owns Slice 2's money path (FD-4, FD-10b), deliberately ahead of claims
Captured: 2026-08-27

## Possible feats

- **One price list, and a cash price stated once** — the schedule of charges plus the practice's self-pay discount as a single dated Promise whose maker is the owner, resolved by date of service, so the desk never negotiates and a mid-cycle price change never reprices a prior estimate.
- **The estimate is arithmetic; the Good Faith Estimate is a document** — one computation with two destinations, split by a boolean the desk already knows (will a claim be filed?), so the spoken range for an insured patient never acquires legal weight and the written estimate for an uninsured one never degrades into a conversation.
- **The dispute is a case with a deadline** — the Good Faith Estimate dispute as an enumerated non-visit Thread that borrows its dollars from the disputed difference, carries the patient's window as its clock, applies `disputed` to the visit it references, and assembles its evidence file entirely from documents already frozen.
- **Collect where the patient already is** — card at the desk, cash into a counted drawer, and a texted pay link that needs no account, all three producing one receipt, one allocation at capture, and one Money Line, so unapplied cash is structurally impossible on this path.
- **The same-day close for a visit with no payer** — `charged → banked → closed` before the patient reaches the parking lot, because the only thing standing between charged and closed on this path is a payer that does not exist.
- **Every self-pay dollar has a destination** — a reason on every line, a courtesy discount that is booked as a discount and not as a write-off, sales tax where the practice sells a thing rather than a service, and a drawer that ties into the day's close or prints the difference unresolved.
- **The coverage that arrives late, both directions** — a self-pay patient who turns out to be insured, and an insured patient whose coverage is retroactively terminated, each handled as a dated reversal and a reopened Thread rather than as an edited record.

## Focus

What is the smallest complete money path for a patient with no payer in it —
priced, discounted, promised in writing where the law requires it, collected at
the desk, receipted, posted to a destination account, and closed the same day —
and exactly where does the line fall between a number the practice *says* and a
number the practice *signs*?

## Current state: the eight percent that is outside the model

Slice 2 exists because
[Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md)
found that **every path in the revenue-integrity cluster begins with a claim**.
Its D5 states the consequence plainly: "a practice with 8% self-pay has 8% of its
revenue outside the product's entire integrity model, including its discount
policy, its receipts, and its GL destination." The same review's F3 adds the
category the gap actually covers — "anything that never becomes a claim: cash
patients, no-show fees, records fees, cosmetic or non-covered services" — and its
F2 notes that "the practice's own discount policy is a reimbursement term too and
has no home."

The revenue-cycle review reached the same place from the patient side. PB-2
graded estimates essential-core *with a compliance floor* and gave the split this
document formalizes: "an *insured pre-service estimate*, which is a range used in
a front-desk conversation, and a *self-pay Good Faith Estimate*, which is a
document with required elements, delivery timing, and a retained copy" — and
warned, "do not blur them." PB-6's first alternative is blunter still:
"**collect at the desk. Time-of-service collection beats every dunning strategy
on cost and yield**," which makes statements a backstop rather than a program.
PB-3 named the discipline that keeps this path clean — every payment carries "its
**allocation intent at capture time**… deferred allocation is how unapplied cash
accumulates and how reconciliation dies" — and PB-5 supplied the asymmetry that
governs how much to collect: over-collecting produces "a refund, a phone call, a
check, and a reconciliation item," while under-collecting produces a statement.

FD-4 ratified all of it as one path — *estimate → GFE where required → collect at
the visit → receipt → thread closed same day* — with an explicit ceiling on
sophistication: "beta estimates are schedule-of-charges arithmetic plus
promise-memory copay/deductible hints, not benefit-engine modeling."
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)
then placed it in **Slice 2, ahead of claims**, for two reasons that are also this
document's design constraints: it is the *simplest* money path — no payer, no
clearinghouse — and it is the only way the beta collects a dollar before it can
bill anyone.

**The sequencing has one consequence nobody has yet stated.** Slice 2 precedes
Slice 3, and promise memory (FD-2) is a statistic over remits that Slice 3
generates. At Slice 2, n = 0. So the copay-and-deductible half of FD-4's estimate
does not exist yet — and cannot be faked without violating R-4's own instruction
to "activate progressively as n grows rather than pretending confidence at n=1."
This is not a problem; it is a clarification of what Slice 2 ships. The Good Faith
Estimate needs **no promise memory at all**, because a self-pay patient has no
payer to remember. It is arithmetic over the price list and the discount, and it
is complete on day one. The insured quote is *almost entirely* promise memory, and
in Slice 2 its only honest output is a sentence that says so.

**Slice 2 therefore ships the Good Faith Estimate whole and the insured quote as a
truthful stub.** That is the correct order, and it is the strongest argument for
the split this document draws.

## Proposed feature set

### 1. One price list, and a cash price stated once

The practice has two numbers per service, not four hundred rows of contract data:
**what we charge** and **what we take from someone paying us directly**. Both are
Promises in the FD-4 / A-1 sense — Declared, with a maker and a document — and
both are resolved by date of service.

| Promise | Maker (N-1: who *asserted* it) | Evidence | Resolved by |
|---|---|---|---|
| **The schedule of charges** | the practice, dated, one publish action | the prior year's own charge lines | date of service |
| **The cash price** (self-pay discount) | the owner, dated, one sentence and one number | the policy statement itself | date of service |
| **A single-patient price** (hardship, an agreed package) | the owner, dated, scoped to one party | the note that authorized it | date of service |

**The discount is a policy, not a decision.** The frame rule (FR-1) is decisive
here: at two to four staff there is no financial counselor, and a desk that is
allowed to negotiate will negotiate differently on Tuesday than on Thursday. The
practice states its cash price once — "self-pay patients pay 60% of our charges,"
or a flat price per service — and the desk applies it. A price outside the policy
is a *second Promise* with the owner as maker, dated and scoped to one patient,
not a field the desk edits.

**The price list is seeded, not authored** (FR-8). The setup artifact is the
practice's own last twelve months of charge lines: the top forty codes by
frequency and what was charged for each. The discount is seeded the same way —
"you collected a median of 62% of billed charges from self-pay patients last year;
should your cash price be 60%?" — which converts a policy the practice has never
articulated into a number it can confirm or correct. Where no history exists, the
public Medicare fee schedule times a multiplier is the fallback, exactly as CC-1
proposes for the charge master.

**Licensing, and a finding it forces** (FR-7). The price list is keyed by
procedure code, and procedural codes are the licensed overlay. The core ships the
*structure* plus the license-free sets; the practice imports its own CPT® overlay
at first run. This matters more here than anywhere else in the beta, because a
Good Faith Estimate must carry a **description of the service and its service
code** — and the description is the licensed half.

> **The document prints the practice's own plain-language service name as the
> description, with the code beside it.** "Office visit, 30 minutes, established
> patient — 99214." That is what the regulation actually wants a patient to read,
> it is what the desk already says out loud, and it means a practice that has not
> yet loaded its overlay can still price, estimate, collect, and issue a compliant
> document. Copying the licensed descriptor onto a patient-facing page would be
> the one place the beta shipped licensed content by accident.

**Price changes are scheduled, never applied.** A price change is a future-dated
Promise on the **Scheduled Changes Calendar** with one-click cancel — which is
governance tier 3's shape, arrived at from the other direction. Effective dating
never appears as a user-facing concept: the manager only ever says "starting on
___" or asks "as of ___."

**The valve:** the effective price on a date of service is what the estimate
arithmetic reads, what the receipt prints, and what the dispute file cites. When a
change lands, it **invalidates every issued estimate whose date of service falls
after the effective date** and those appear as work — before the patient is
billed, never after. That is the same invalidation cascade CA-3 describes for
coverage, applied to price.

### 2. The estimate-versus-GFE split

This is the document's central line, and it is drawn on a boolean the desk already
knows.

> **If a claim will be filed for this visit, the number is a quote.
> If no claim will be filed, the number is a Good Faith Estimate and it is issued
> as a document on a clock.**

Everything else follows from that one test.

| | **The quote** (insured) | **The Good Faith Estimate** (uninsured / self-pay) |
|---|---|---|
| What it is | a spoken or on-screen range, with an as-of and a sample size | a written document with required elements |
| Is it a Promise? | **No.** It is a *rendering of promise memory*, like a weather forecast | **Yes** — Declared, maker = the practice, frozen at issuance |
| Its arithmetic | the allowed amount from promise memory, minus what the plan pays, plus copay and deductible hints | the price list, minus the cash price discount. **No payer, therefore no promise memory** |
| Needs Slice 3? | yes — at n=0 its only honest output is "we won't know until your plan processes it" | **no** — complete in Slice 2 |
| Frozen on the Thread? | only if it was *delivered* in writing | always ("the estimate exactly as delivered") |
| Its clock | none | days remaining to issue, from the booking transition |
| Variance watched? | as a product-quality metric, aggregated | **per document**, against a $400 threshold, with legal consequence |
| Retention | ordinary | six years, and producible on request |
| What it can become | a statement, an argument, a refund | a dispute |
| Governance tier | 1 | **2 — do + notify** (see below) |

**Why the quote is not a Promise.** A Promise with a maker creates an obligation
the practice can be held to, and holding the desk to a benefit computation it did
not perform — over accumulators the payer itself reports stale — is a trap. CA-2's
verdict is the governing one: "a patient quoted $40 who owes $312 costs more trust
and more collection effort than a patient quoted '$40–$300, we won't know until
your plan processes it.'" The quote's whole obligation is to be **honest about its
error bar**. Its shipped form is one line with three parts — a range, a count, and
a caveat — and at n=0 the range is absent and the caveat is the entire sentence.

**Why the GFE is unambiguously a document.** For uninsured and self-pay
individuals the written Good Faith Estimate is a legal obligation under the No
Surprises Act, not a feature choice — the review's phrasing is that a product
touching self-pay billing without one "is exporting risk to the customer." Three
facts about it drive the design:

- **Issuance is triggered by scheduling or by request, not by the visit.** The
  shipped rule set: scheduled at least three business days ahead → issue within
  one business day of scheduling; scheduled at least ten business days ahead →
  within three business days; requested by the patient → within three business
  days of the request; **scheduled fewer than three business days ahead → not
  required** (though the practice should still hand over a price).
- **"Self-pay" includes an insured patient who elects not to use their
  insurance** for this service. That flip is common — a cash-price physical, a
  non-covered cosmetic service, a patient with a high deductible doing arithmetic
  at the desk — and it is the single most-missed trigger.
- **The content is enumerated**: the patient's name and date of birth; a
  description of the primary service and its scheduled date; an itemized list of
  the items and services reasonably expected, with diagnosis codes, service codes,
  and expected charges; the name, NPI, TIN, and location of each provider; a list
  of anything anticipated to require separate scheduling with the notice that
  separate estimates will follow; the notice of the dispute process if billed
  charges exceed the estimate by at least **$400**; and the notice that the
  estimate is not a contract and does not obligate the patient to obtain care.

**Delivery is evidence, not a send.** The document is issued in a form the patient
can understand and in a way that produces a record: handed over and signed at the
desk, texted or emailed with a delivery receipt, or mailed. The GFE register
stores the artifact, the channel, the timestamp, and — where the patient signed —
the signature. A GFE nobody can prove was delivered is a GFE that was not issued.

**Corrections are supersession.** A GFE is never edited. A changed service, a
changed price, or a changed provider produces a **new GFE that supersedes the
prior one**, both retained, both linked, and the dispute threshold measured
against the **most recent one issued before the service**. This is FD-3 applied to
a document rather than to a ledger row.

**Governance tier 2 — do + notify**, and the reasoning matters. Tier 3 exists for
things leaving the building that benefit from a cancel window. A GFE benefits from
the opposite: it has a one-to-three-business-day clock, and FR-6's own rule is
that a gate which would push a Thread's clock past zero is not a control. So the
GFE issues immediately, is reversible by supersession, and appears in Today and on
the day's close sheet. Nobody waits, and nobody misses it.

**The honesty asymmetry, stated once so the design does not drift.** The GFE's
*legal* risk runs one way only — being too **low**, since only billed charges
exceeding the estimate by $400 open a dispute. Its *commercial* risk runs the
other way: an inflated estimate collects less, refunds more, and destroys the
trust that makes desk collection work at all. The resolution is not a fudge
factor:

> **Estimate at the price. Collect at the price. Where a service might change
> during the visit, estimate the range's top and collect its bottom, and say so.**

PB-5's preventive rule is the same instinct — over-collecting produces a refund,
a phone call, a check, and a reconciliation item, and the asymmetry is large.

### 3. The dispute clock as a Thread

The Good Faith Estimate dispute is **thread type 6** in the visit-thread contract,
already enumerated, already closed to extension. This document names what fills
its five properties and what it is allowed to do to the visit it references.

| Property | Value |
|---|---|
| **Station** | `intent` (the patient says the bill is wrong) → `claimed` (our documentation left the building to the dispute entity) → `remitted` (a determination arrived) → `banked` where money moves, or straight to `closed` where the balance simply resolves |
| **Clock** | the nearest deadline that costs money — the patient's window to initiate, then **our** window to submit documentation once the resolution entity asks |
| **Dollars** | the disputed difference, direction outbound, counterparty the patient |
| **Owner** | the manager, always. Never the biller, never "the desk" |
| **Links** | *spawned from* the visit Thread; *held by* nothing |

**What it does to the visit Thread is the whole point.** It applies the
`disputed` condition, and the visit-thread contract already states what `disputed`
means: "while a GFE dispute or an appeal is open on money, no statement for that
money leaves the building, and no collections cadence advances." That is the valve
the feat review found had no consumer — statement-versus-dispute, gap 10 in the
revenue-integrity cluster.

**The dispute file is assembled, not authored** (FR-4). Every element already
exists as a frozen artifact, which is the only reason a one-click packet is
possible:

| In the file | Where it comes from |
|---|---|
| the Good Faith Estimate exactly as issued, and every superseded version | the GFE register |
| the delivery record — channel, timestamp, signature | the GFE register |
| the itemized bill exactly as sent | the Thread's frozen copy |
| the price list as of the date of service | the Promise, as-of query |
| the cash price policy as of the date of service | the Promise, as-of query |
| every receipt and every reversal on this visit | the Money Ledger |
| the services actually rendered, by reference | the charge, citing the EHR fact |

**The threshold is a valve, and it fires before the bill goes out, not after.**
When a bill is built for a visit that carries an issued GFE and the billed total
exceeds the estimate by $400 or more, the bill **does not leave the building
silently**. It surfaces as one line in Today with the two numbers and the
difference, and the desk's three options are the honest ones: bill the estimate,
bill the difference with a written explanation, or bill it as-is and let the clock
start. This is the cheapest possible dispute prevention and it costs one
comparison.

**The clearing conditions** (FR-5), none of which is "a human marks it done":

| Work | Clears when |
|---|---|
| "we owe this person a written price" | a GFE is issued *and* its delivery is evidenced |
| "this bill is more than $400 over what we told them" | the bill is revised under the threshold, the patient's window expires, or a dispute resolves |
| "the dispute entity asked us for documents" | the file is submitted and acknowledged |

### 4. Collecting at the desk, on a link, and in cash

Three moments, one payment object, one receipt shape. PB-3's finding governs:
"the design decision that matters is not the method list but the *moment*."

| Moment | Instrument | Money Line at capture | Landed when |
|---|---|---|---|
| At the desk, patient present | card-present terminal | the day's card batch — **expected** | the batch funds, T+1 or T+2 |
| At the desk, patient present | cash into the drawer | the drawer — **landed at the count** | immediately; it is countable |
| At the desk, patient present | check | the deposit — **landed when banked** | the deposit slip's date |
| From a texted link, no account | hosted payment page | the online batch — **expected** | the batch funds |

**The pay link needs no account** (FD-10b). It is the patient money view: what you
owe, why, and pay now, behind a URL the desk texts in two taps. It is beta scope
even though the companion app is not, because it is where patient dollars actually
come from and because a self-pay patient who declines at the desk is otherwise a
paper statement and a phone call — the exact incumbent experience the packet
promises to beat. The link carries **no product branding that could be mistaken
for the deferred app's name** (R-8).

**One rule keeps this path clean forever:** *every payment carries its allocation
at capture.* Which visit, which price line, which installment. PB-3 is explicit
that "deferred allocation is how unapplied cash accumulates and how reconciliation
dies," and on the self-pay path deferred allocation is not merely bad practice —
it is unnecessary, because the patient is standing there and the visit is known.
**Unapplied cash is structurally impossible on this path.** Where money arrives on
the link with no visit attached — a patient paying an old balance, a payment from
a name the system does not recognize — it does not become a self-pay receipt; it
mints a **credit-balance Thread** with cause *unidentified receipt*, which is the
other document's problem and this document's boundary.

**The receipt is an artifact, not a confirmation screen** (FR-4). One shape,
printed or texted, and it carries four things: what the service was in plain
language, the price, the discount named as a discount ("our cash price"), and what
remains — which for a pure self-pay visit is zero and should say so. Where a
Good Faith Estimate was issued, the receipt cites it: *we told you $185 on Aug 18;
you paid $185.* That single line pre-empts the dispute that PB-1 identifies as the
leading cause of patient billing calls.

**Governance, per action:**

| Action | Tier | Undo means |
|---|---|---|
| take a payment, allocated at capture | 1 | append the reverse; silent |
| apply the published cash price | 1 | it is the policy, not a decision |
| price outside the published policy | 2 | recorded, named on the close sheet and in the owner's week |
| void a payment before the batch closes | 1 | the batch never carried it |
| issue the Good Faith Estimate | 2 | supersede with a corrected document; both retained |
| bank the drawer, print the deposit slip | 2 | it is a Money Line the practice created |
| **refund money to the patient** | **4** | money leaving the practice; a new dated transaction, never a rewind |
| write off a self-pay balance above the small-balance threshold | 4 | as above |
| submit the dispute file to the resolution entity | 3, with the deadline escape | cancel inside the window; after that it left the building |
| change the price list | 3 via the Scheduled Changes Calendar | cancel the scheduled change |

### 5. The same-day close, and the thread that does not close

For a pure self-pay visit the transition table already has the move:
`charged → banked` — "Self-pay, paid at the desk — no payer, no claim" — followed
by `banked → closed`. **The only thing standing between charged and closed on this
path is a payer, and there isn't one.** Tyler Vance signs at 9:34am, pays $185,
takes a receipt, and his Thread is finished before he reaches the parking lot. It
appears on the day's close sheet under "Paid in full at the desk, no claim" and
never appears again.

**Three cases do not close the same day, and each has a defined home:**

| Case | What happens |
|---|---|
| The patient declines to pay | Thread stays at `charged`, dollars owed by the patient, direction inbound. It enters the statement path and is no longer this document's problem. **Nothing about declining is an error state** — it is a disposition with an owner. |
| The service changed during the visit | The price changed, so the GFE is superseded *before* the bill, not after. If the new total crosses $400 over the last-issued estimate, the pre-bill valve fires. |
| The patient pays part | Allocated at capture to the named lines; the remainder is a balance. A plan is the shared payment-plan object, in three shipped shapes, never a designer at the desk. |

**A discrepancy this document must record, not resolve.** The visit-thread
contract defines `banked` as "Money moved and a bank line proves it," while the
Money Line contract (C-3) puts a card batch closed at 5:20pm in the **expected**
state until it funds T+2. Read literally, a self-pay card payment cannot reach
`banked` on the same day, and FD-4's "thread closed same day" would be
unachievable for every payment except cash.

The proposed reading — offered as a **clarification for the Thread contract's
owner to accept or reject, since visit-thread governs thread mechanics and a
sibling document may not amend it** — is that `banked` asserts *the money is on a
Money Line*, not that the Money Line has landed. The bank-line proof lives on the
Money Line and travels expected → landed → explained there; the Thread does not
wait for it. Two things argue for this reading. First, the contract already
enumerates `closed → any prior station` for "a late ERA, **a chargeback**, a
recoupment" — which means a closed thread was never understood to imply settlement
finality, or the chargeback reopen would not need to exist. Second, the alternative
makes the close sheet unprintable: the prototype's Tuesday sheet closes at 5:41pm
with the day's card batch on it, and no version of that page can wait until
Thursday.

Recorded in Open questions either way.

### 6. Where each dollar lands

The reason taxonomy is the join key to the general ledger, it is defined before
anything posts (Slice 0), and it is unfixable downstream. This document
contributes the rows a claim never produces.

| Reason | Destination | Note |
|---|---|---|
| self-pay service revenue | patient service revenue | the full charge, always at gross |
| **cash-price discount** | **contra-revenue** | *not* a write-off; it was granted before the obligation existed |
| single-patient / hardship price | contra-revenue, separately named | so the owner can see how often it happens |
| small-balance write-off | write-offs | after the fact, under threshold, tier 2 |
| bad debt / charity | separate accounts | different reporting consequence; F7's taxonomy |
| no-show fee | patient service revenue, own line | never billed to a payer, always self-pay |
| records fee, forms fee | non-patient-service income | F7's "money that arrives without a patient" |
| retail, supplements, cosmetic goods | product revenue, **plus sales tax where applicable** | the one place the practice sells a thing, not a service |
| patient refund | reduces revenue or clears the liability | tier 4, and it is a Money Line leaving |
| patient credit held | **liability**, not revenue | it is somebody else's money |
| merchant fee on a card payment | expense | the gross-versus-net seam; it belongs to Money Lines |
| chargeback, and the chargeback fee | contra-revenue and expense | reopens the Thread |

**The distinction that carries the most weight is discount versus write-off.** A
cash-price discount is granted *before* the patient owes anything: the obligation
was $185, never $308. A write-off is granted *after*: the patient owed $308 and we
gave up on $123. They map to different accounts, they have different tax
treatment, they tell the owner different things about his own practice, and a
generic "adjustment" destroys the export. This document's contribution to the
taxonomy is that both rows exist and the surface never offers a choice between
them — the *moment* determines which one is written.

**The drawer, and the day's close.** The self-pay path is the reason the drawer
exists in the model at all, and the drawer is the small practice's most common
integrity failure. The **Daily Balanced Day** section of the close sheet is the
control point, and the design rule from the paper prototype is that it prints
*unresolved, then resolved*, because that is what proves the check ran:

```
  Card terminal batch, closed 5:20pm .................  $437.00
  Card payments we had posted ........................  $412.00
  Difference .........................................  $ 25.00
       FOUND 5:34pm.  Sam Okonkwo's copay taken on the Pine Ridge reader
       at 9:45 and never posted.  Posted now.  Difference $0.00.

  Cash drawer   float $150.00 + cash taken $85.00 = should be $235.00
                counted $235.00                    over/short   $0.00
  Deposit slip printed: $145.00, cash and check, to the bank Wed Aug 26.
```

**openPractice generates the deposit slip**, which is the trick that makes cash
reconcile by construction: if the product defines the deposit, the deposit
matches. The counter is a Roster entry, named on the sheet. An unexplained
over/short does not block the close — blocking it would teach the practice to
fudge the count — but it prints, it carries a name, and it appears on tomorrow's
huddle until it is explained.

### 7. When the coverage turns up late

Both directions happen, both are common, and neither is an edit.

**Self-pay who turns out to be insured.** Retroactive Medicaid, a card found in a
coat pocket, a spouse's plan the patient forgot. Five things happen in order, and
none of them deletes anything:

1. **The GFE stands.** It was correct on the date it was issued, when the patient
   was self-pay. It is evidence, it is retained, and deleting it would falsify the
   record. Its variance watch **ends**, because the patient is no longer being
   billed as self-pay.
2. **The discount is reversed.** A payer is billed the practice's standard charge,
   not its cash price. The contra-revenue row is reversed with a date and a
   reason — never edited away.
3. **The money the patient paid becomes a prepayment**, allocated against whatever
   patient responsibility adjudication eventually produces. Where it exceeds that
   responsibility, it mints a **credit-balance Thread** with cause *patient paid an
   estimate that exceeded actual responsibility* — the first cause Slice 2 seeds,
   exactly as the sequencing document says.
4. **The Thread reopens** — `closed → charged` — and walks forward normally to
   `claimed`. A dated forward move, attributed, never a rewind.
5. **The receipt is not reissued.** It said what it said on the day. A second,
   dated receipt supersedes it and both are retained.

**Insured who turns out to be self-pay.** Coverage retroactively terminated, a
denial for no active coverage, a plan that ended on the first of the month. This
is the harder direction and the one that generates disputes at a high rate,
because the patient was quoted a copay and is now looking at a full charge.

- **No GFE obligation attaches retroactively.** The trigger is the patient's
  status at scheduling, and at scheduling this patient was insured. The practice
  did nothing wrong by not issuing one.
- **But the cash price does apply.** The self-pay discount is a Promise the
  practice already made to *everyone in that class*, and a patient who lands in
  the class through no act of their own lands in it fully. The shipped behavior is
  that the discount is applied **automatically** and the revised balance is what
  the patient ever sees.
- **The balance does not enter a cadence at full charge, ever.** That is a valve:
  coverage terminated → discount applied → statement built. A statement that
  crosses that sequence out of order is the single most reputation-damaging thing
  on this path.
- The Thread walks `remitted → charged` and the patient's own bill page tells the
  story in the plain-language labels the contract already requires.

**The inducement caveat, named because it is easy to get wrong.** Discounts
offered to beneficiaries of federal programs raise inducement questions that a
blanket policy will not answer. The **shipped default is the narrow one**: the
cash price applies to patients with *no coverage for the service*, and a
federal-program beneficiary electing to self-pay is a case the practice decides
deliberately with the owner as maker, not a case the desk applies automatically.
Flagged for counsel alongside R-1.

### 8. Naming (FR-2)

None of this document's vocabulary reaches a surface.

| Never on a surface | What the page says |
|---|---|
| Good Faith Estimate | *we owe him a written estimate* / *the price we gave you in writing* |
| patient-provider dispute resolution | *he says the bill is $400 more than we told him* |
| schedule of charges | *our prices* |
| self-pay discount policy | *our cash price* |
| time-of-service collection | *he paid at the desk* |
| courtesy discount vs. write-off | *our cash price* vs. *we gave up on this* |
| over/short | *the drawer is $25 short* |
| contra-revenue, GL destination, reason taxonomy | *(never user-facing at this size at all)* |
| unapplied cash | *money in, we don't know whose* |

The test both ways: the newest person at the desk can explain the page to a
patient in one sentence, and the office manager would say the page's name out
loud. "The written estimate" passes. "The GFE register" does not.

### 9. What this document deletes

The acceptance test's third question, answered:

- **the benefit-estimation engine** — replaced by arithmetic plus an honest error
  bar (FD-4's ceiling);
- **the financial-counseling workflow** — there is no counselor at four staff;
- **the discount approval queue** — the discount is a policy with a maker, so
  there is nothing to approve;
- **the sliding-fee-scale authoring surface** — FQHC-specific, post-beta unless the
  design partner is in that segment;
- **a separate self-pay ledger** — there is one immutable ledger with reasons;
- **the payment-plan designer at the desk** — three shipped shapes, shared with
  patient billing;
- **any surface that asks the practice to choose between "discount" and
  "write-off"** — the moment determines it.

## Interfaces and boundaries

**Consumes.** The party register for the patient, the guarantor, and the
*uninsured-or-self-pay status on a date* (a party fact, per A-4, not a noun). The
Roster for the person who took the payment and the person who counted the drawer.
The Thread for the visit this money belongs to and for the dispute case. The
Promise for the price, the discount, and the GFE itself. Promise memory (FD-2) for
the insured quote's hints — **not available in Slice 2, and the quote says so**.
Charges for what was actually rendered. The Scheduled Changes Calendar for future
prices. The code-set overlay (FR-7) for the code beside the plain-language
description.

**Emits.** Issued Good Faith Estimates with delivery evidence and a six-year
retention floor. Receipts. Money Lines in all three states — the counted drawer
landed, the card batch expected, the check landed when banked, the online batch
expected. Immutable ledger rows, each with one reason and one destination account.
The generated deposit slip. The close sheet's money section. The dispute file. A
`disputed` condition on a visit Thread, and the statement suppression that
follows from it. Credit-balance Threads with cause *we over-collected at the desk*.

**Owns.** Pricing and discounting for money with no payer in it. The Good Faith
Estimate as a document, its register, its variance watch, and its dispute case.
Collection at the three moments of the visit. The receipt. The drawer. The
same-day close for a self-pay visit. The retroactive-coverage flip in both
directions.

**Does not own, and the seams are declared:**

| Not ours | Whose | The seam |
|---|---|---|
| the bank line, merchant fees, the gross/net split, chargeback mechanics | **Money Lines And The Bank Inbox** | we emit Money Lines in three states; they explain them to zero |
| the day's open and close, the huddle page, the signed sheet | **The Day Rhythm** | we add exactly one section to the close sheet |
| the statement cycle, dunning cadences, the balance across claims | patient billing | our `disputed` condition suppresses their statement; their patient money view renders our receipts |
| what a payer would have allowed | promise memory, Slice 3 | the insured quote reads it; the GFE never does |
| refund disbursement, escheatment, the 60-day clock | **Refund Disbursement Register** | we mint the credit; they issue and clear it |
| the charge itself and its codes | charge capture | we price what they produced |
| the clinical fact | the EHR, always | we price the administrative consequence |
| the pay link's balance-and-why half across all visits | patient billing / the patient money view | we own *pay now at the visit*; they own *what you owe overall* |

**The estimate's boundary with the law is narrower than it looks.** The Good Faith
Estimate for uninsured and self-pay individuals is in force and is what this
document ships. The **advanced explanation of benefits** for insured patients —
the insured analogue, which would make the quote a document — has not been
implemented through rulemaking. That is precisely why the quote can remain a
conversation, and it is the fact this split is built on. **If that changes, the
split changes**, and it changes in a knowable direction: the quote acquires a
maker, a document, a delivery record, and a clock, and becomes a second instance of
machinery this document has already built. Designed for, not built now.

## Alternatives and tensions

**One estimate object versus two.** Modelling the quote and the GFE as one thing
with a flag is tempting and cheaper by a screen. It is also how the compliance
floor gets lost: a flag can be false by default, and an estimate that is
*sometimes* a legal document is a document nobody treats as one. Two objects with
one shared arithmetic costs a little duplication and buys the property that the
document half cannot be forgotten. The price is real — a service that changes
mid-visit for a patient who flips from insured to self-pay crosses the boundary,
and that crossing has to be a deliberate, dated act rather than a field edit.

**A cash price that is a policy versus a price the desk can set.** A stated policy
deletes an approval queue, a negotiation, and an inconsistency the practice cannot
see. It also means the first patient who asks for a break gets "our cash price is
$185" and nothing else, until the owner makes a second Promise. Some practices
will experience that as rigidity. The counter-argument is that the alternative —
a desk that discounts case by case — is exactly the surface FR-1 exists to delete,
and the escape (a dated, scoped, single-patient Promise) is one action away.

**Collecting the full price at the desk versus collecting conservatively.** For a
pure self-pay visit these are the same number and there is no tension. The tension
appears only where the service might change, and it points opposite to the insured
case: PB-5 and F10 both argue for collecting the *low* end of a band because
over-collection produces a refund, a phone call, a check, and a reconciliation
item. That advice is correct for insured patients and wrong for self-pay ones,
where the price is known and under-collecting simply manufactures a statement for
money the patient was standing there ready to pay. The rule this document takes —
estimate the top, collect the price — is the resolution, and it should be measured
rather than asserted: if refunds caused by desk over-collection exceed a handful a
month, the rule is wrong.

**A same-day close versus settlement truth.** Closing a Thread whose card money
has not funded is closing on the patient's reality rather than the bank's. That is
the right frame for the *patient's* story and it is what makes the close sheet
printable at 5:41pm — but it means a chargeback reopens a closed thread six weeks
later, and a product that promised "closed" has to be willing to say "reopened."
The contract already carries the machinery; the discomfort is real and belongs in
the naming, not in the model.

**The GFE's variance watch is a promise the practice can fail publicly.** Watching
billed-versus-estimated and surfacing the $400 crossing before the bill goes out
is the correct behavior and it will, some months, tell the owner that his own
estimates are bad. That is the feature working. The failure mode is a practice
that responds by inflating every estimate, which is legally safe, commercially
corrosive, and invisible unless the *median* absolute variance is measured
alongside the count of crossings. Measure both or the metric teaches the wrong
lesson.

**Six years of retained documents in an append-only history.** The GFE retention
floor and R-6's unresolved deletion problem meet here directly: a patient-requested
restriction, a minor's record rule, or a data-minimization obligation may require
removing content from a structure this packet has defined as immutable, and the GFE
is a document with a statutory retention floor underneath it. The two constraints
are not obviously compatible and no ratified decision covers the collision.

**The insured quote ships as a stub, and stubs are load-bearing.** Slice 2's honest
"we won't know until your plan processes it" is correct and will be read by some
observers as the feature being absent. The mitigation is that the sentence is
*designed* — it names what is unknown, it names when it will be known, and it is
replaced by a range the week promise memory reaches a defensible n, without any
consumer changing.

## Open questions

- **Does `banked` require a landed bank line?** The visit-thread contract says
  "money moved and a bank line proves it"; C-3 says a card batch is *expected*
  until it funds. Under the literal reading, FD-4's same-day close is achievable
  only for cash. This document proposes that `banked` asserts the money is on a
  Money Line and that the landing lives on the Money Line — supported by the
  contract's own `closed → prior station` chargeback reopen — but the Thread
  contract governs thread mechanics and must settle it. **Recorded as a
  discrepancy, not resolved here.**
- **The GFE dispute type's enumerated station walk** in the visit-thread contract
  reads `intent → claimed → remitted → banked → closed`, but a dispute resolved by
  reducing a balance moves no money and should terminate at
  `remitted → closed` — a transition the table already permits generally.
  Proposed as an editorial correction to the type's walk, flagged rather than made.
- **The paper prototype's GFE clock is attached to the wrong event.** Page 1 shows
  Tyler Vance's written estimate as due **TODAY, before he is seen**. The statutory
  clock runs from *scheduling*, not from the visit — so a GFE for an appointment
  booked a week earlier was due days ago, and a GFE for an appointment booked
  yesterday is not required at all. Either the prototype's line describes a
  practice convention rather than the legal deadline (in which case it should say
  so and not appear under "clocks running out"), or the clock is misplaced.
  Settle before the huddle page is staged.
- **What exactly are the current issuance windows, content elements, and dispute
  timings?** The rules stated above are the design's assumption. They require
  verification against current CMS guidance — including the standing enforcement
  discretion on co-provider and co-facility content — **before Slice 2 is staged**,
  on the same footing as R-1's counsel verification of CPT licensing. A brainstorm
  document is not a compliance opinion.
- **Does the co-provider element ever bind this practice?** A small ambulatory
  practice acting as a convening provider for a service involving an outside lab,
  imaging center, or anesthetist would owe a combined estimate. Whether the beta
  practice's service mix ever triggers that is a design-partner question and
  changes whether the GFE is a one-provider document or an assembly.
- **Is the insured quote ever delivered in writing in the beta?** The visit-thread
  contract freezes "the estimate exactly as delivered." If the quote only ever
  appears on the visit task list, it is delivered — which means the freeze applies
  and the quote acquires evidentiary weight this split says it should not have.
  Either the quote is spoken only, or the split needs a third row.
- **What is the small-balance write-off threshold, and who sets it?** It
  determines the tier boundary between 2 and 4 on a self-pay balance, and FR-1
  says the practice should not have to author it. Ship a default and measure.
- **Should a self-pay patient's collected money be refundable to the original card
  automatically?** F10 notes a card payment should usually be refunded to the
  original method, which is a merchant operation with its own fee behavior — and
  it is money leaving the practice, so tier 4. Whether the card refund is one
  tier-4 action or two is unsettled.
- **Retention versus deletion, this document's instance of R-6.** A six-year
  document retention floor inside an append-only chain that must sometimes honor a
  deletion obligation. Decide in the ledger design in Slice 0, before there is a
  year of history to invalidate.
- **Does the tier-3 dispute submission collide with the constitution's Principle
  II** the way R-2 describes for claim batches and statements? A documentation
  package leaving the building after a cancel window with no affirmative click is
  the same reading, applied to a smaller thing. Record it in the first proposal's
  Constitution Check.

## Relationships

Owns **Slice 2** of
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) —
"Money at the desk" — and the four capabilities that slice names: **Self-Pay And
Time-Of-Service Money Path**, **Good Faith Estimate Register And Dispute Clock**,
**Price-By-Date-Of-Service**, and the visit-side half of **Patient Money View**,
each defined in the capability register of
[Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md),
whose eight-rule feature contract (FR-1 … FR-8) this document is written against.

Bound by [The Visit Thread](openpractice-small-practice-beta-visit-thread.md),
which is authoritative for thread mechanics: the `charged → banked → closed`
self-pay move, the `disputed` condition and its statement suppression, non-visit
thread type 6 (GFE dispute), the reopen path a chargeback or a late coverage
discovery takes, and the freezing rule that makes the dispute file assemblable.
Two discrepancies with that contract are recorded above rather than resolved.

Sibling Tier-1 atomics this document meets at a declared seam:
[The Day Rhythm](openpractice-small-practice-beta-day-rhythm.md) — this document
adds exactly one section to the close sheet, and the drawer count is its control
point; [Money Lines And The Bank Inbox](openpractice-small-practice-beta-money-lines-and-bank-inbox.md) —
we emit Money Lines in three states, they explain every bank line to zero, and the
merchant fee, the chargeback, and the gross-versus-net split are theirs;
[Phone, Fax, And The Inbound Pile](openpractice-small-practice-beta-phone-fax-and-inbound-pile.md) —
a mailed dispute notice, a returned refund check, and a patient's written price
request arrive there as Threads before they arrive here as work.

Governed by [Framing Decisions](../review/openpractice-framing-decisions.md):
FD-4 (this document's charter), FD-10b (the texted pay link, no account), FD-5
(the four tiers and the deadline escape), FD-3 (corrections as dated reversals),
FD-2 (promise memory as the quote's source, and its absence in Slice 2), FD-1 (the
2–4 staff frame that deletes the counselor and the approval queue), FD-11 (the
licensed overlay, and why the GFE prints a plain-language description). Implements
the paper-prototype amendments through the Thread contract, and takes its close
sheet, drawer, and deposit-slip shapes from
[Paper Prototypes](../review/openpractice-paper-prototypes.md) Page 2 and Page 3.

Answers the findings in
[Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md)
— D5's cash-patient integrity hole, F2's homeless discount policy, F3's "anything
that never becomes a claim," F7's reason taxonomy, F10's over-collection
asymmetry, and cross-feat gap 10 (statement versus dispute) — and the
patient-billing feats in
[Revenue Cycle](../review/openpractice-feat-review-revenue-cycle.md): PB-1 (one
understandable balance, and the receipt line that pre-empts the call), PB-2 (the
split this document formalizes), PB-3 (allocation at capture), PB-5 (corrections
by reversal and the credit clock), and PB-6 (collect at the desk rather than chase).

Supersedes the treatment of self-pay pricing and estimates in
[Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md)
and the discount-policy gap in
[Payer Contract And Reimbursement Integrity](openpractice-small-practice-beta-payer-contract-and-reimbursement-integrity.md);
takes its price-list structure from
[Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md)
and its charge boundary from
[Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md).
No existing file is modified by this document.
