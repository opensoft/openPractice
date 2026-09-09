# Money Lines: Deposits, Settlements, Fees, And The Bank Inbox — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should make the bank statement itself the worklist — every credit and debit owned by exactly one of a closed list of twelve kinds, with only landed-and-unexplained showing red — and should carry the four mechanisms that otherwise make a bank line permanently unexplainable: EFT-to-ERA reassociation on the trace number, a payer-level-adjustment ledger where a takeback becomes a recoupment case and interest becomes named non-patient income, merchant reconciliation that names the gross-versus-net seam and the payer's virtual card as a payer-imposed fee, and a paper intake path that produces the same objects as an 835; with tomorrow's deposit predicted as arithmetic over expected Money Lines, refunds tracked as a liability from a tier-4 approval to a cleared bank debit, and the day balanced at 5:20pm so the month is only printing.

Topics: openpractice-small-practice-beta, money-lines, bank-inbox
Repository context: openPractice — the Slice 4 money capability for the first small ambulatory practice beta
Captured: 2026-08-27

## Possible feats

- **Every bank line has an owner** — the statement as a to-zero worklist where each credit and debit is explained by exactly one thing from a closed list of twelve kinds, plus `unknown`, which is the only red state and the only one nobody may leave alone.
- **The check that ties** — reassociation on the payer's own trace number plus a payer-level-adjustment ledger, so the wire always equals the sum of the claim payments on it plus a short list of named lines, and a deposit never fails to match for a reason nobody can see.
- **A takeback is a case, not a variance** — a `WO` line mints or resolves a recoupment case linked to the visit it recoups, the demand letter it answers, and the future bank line where the offset actually lands, so Tuesday's letter and Wednesday's short wire are one story rather than two mysteries.
- **Gross is not net** — merchant funding, per-batch and monthly fees, reserve holds, chargebacks and their fees, returned items, and the payer's virtual card, each with a destination account, and the fees named out loud because the owner never sees them on his statement.
- **Paper is a channel, not an exception** — a scanned explanation of benefits and the check that came with it produce the same posting, variance, and provenance objects as an electronic remittance, and end on a deposit slip the product printed.
- **Money leaving the practice** — refunds as a liability from a tier-4 approval through issuance to a cleared bank debit, held money carrying days *remaining* by cause, and the offset election that turns a refund into a future takeback.
- **Tomorrow's deposit, before it arrives** — the money-day predictor as the sum of expected Money Lines: arithmetic, not a forecast, and it scores itself against the bank every morning.

## Focus

What must the product carry so that the owner can open his own banking app on a
Friday, see fifteen lines, and find that fourteen of them already have a
one-sentence explanation and the fifteenth has a name and a date on it — and so
that the sentence "collections minus refunds minus fees equals the bank,
difference zero" is provable by construction rather than asserted?

## Current state: the money arrives explained by nothing

Three findings in the review converge on this document, and each of them is a
place where a small practice's money is currently invisible.

**The bank is where the confusion actually lives.** The revenue-integrity
reviewer named "the bank shows $6,412 and I don't know why" the single most
common weekly money confusion in a small practice, and rated the deposit and
settlement work the **highest-value feat in its cluster for the owner-physician**
— higher than the expectation engine, which is intellectually superior but not
*felt*. Its reframe is the one this document is built on: not "group receipts into
settlement events" but **"every line on the bank statement has an owner and a
one-sentence explanation, or it is red."** That reframe collapses deposit
matching, reassociation, merchant fees, chargebacks, takebacks, and returned items
into one finishable task with one visible definition of done, and it is the only
version of financial integrity the owner can verify himself.

**Four mechanisms make a deposit unexplainable, and none of them had an owner.**
The review's cross-feat gaps list them precisely. The remit-total seam: the
claim payments on an electronic remittance and the check amount differ by
payer-level adjustments, and nothing in the packet owned the difference, so it
would surface as an unmatched deposit with no explanation available anywhere in
the system. The gross-versus-net seam: receipts posted at gross can never tie to a
net-funded processor deposit, the difference is an expense rather than an error,
and no object carried a fee at batch level. The non-patient-money seam: interest,
rebates, records fees, settlement checks, and returned refund checks had no entry
point except a patient obligation, so they would accumulate as permanent
unidentified cash — the exact failure the credit-balance work exists to prevent.
And the paper seam: the revenue-cycle reviewer found that paper remittances and
paper checks, **still a meaningful share of small-practice cash**, had no path
producing the same objects as an electronic remittance, which means that share of
cash bypasses the whole money chain and silently breaks every metric built on it.

**The close was the wrong ceremony.** Period close was graded *questionable*: an
accounting ritual imported into a product whose users do not perform accounting
rituals, whose real close happens in QuickBooks, and whose first late remittance or
sixty-day-old chargeback meets the lock and teaches the staff to route around it.
FD-3 ratified the replacement — immutability at post time, dated reversals,
as-of queries, export-batch freeze as the only lock — and named the ritual that
takes its place: **the daily balanced day**, which a small practice can actually
perform and which catches an error in twenty-four hours instead of thirty days.

Two prior documents already fixed most of the vocabulary this one needs.
[Feature Normalization](openpractice-small-practice-beta-feature-normalization.md)
defines the **Money Line** in three states — expected, landed, explained — and
states that only landed-and-unexplained is red; it also names this as the only
home for takebacks, merchant fees, the gross-versus-net seam, chargebacks,
returned items, payer interest, cash float and over/short, and the deposit slip.
[The Visit Thread](openpractice-small-practice-beta-visit-thread.md) defines the
`banked` station as "money moved and a bank line proves it," enumerates the
recoupment case and the credit balance as first-class non-visit threads, and
publishes the re-association key set for the moment a partner strips our
identifier. This document is the Slice 4 capability those two were written for.
It adds nothing to either contract; it consumes both.

## What this document is not

It is not an accounting system. The general ledger, the bank account, taxes,
payroll, vendor payables, and the final financial statements stay in QuickBooks
or its equivalent. It is not a treasury function: openPractice never originates a
payment, never holds bank credentials that can move money, and never nets one
legal entity against another. And it is not a second close — FD-3 already
decided there is no close, and the artifact this document emits monthly is a
one-page certificate, not a lock.

The acceptance test, answered plainly. **Which noun?** The Money Line, primarily;
The Day for the balanced day and the close; The Thread for the cases this work
mints and the `banked` station it writes; The Promise for the payer cycles and the
processor's funding model, both observed rather than configured. **Which
surface?** The Money, primarily; Today for the day's unexplained lines and the
5:20pm balance; The Pile for paper remittances, checks, demand letters, and
virtual-card faxes; The Thread for a case. **What does it delete?** The deposit
and settlement workbench as a batch-matching engine; the period-close ceremony;
the unapplied-cash aging report; the export acknowledgment state machine; and the
phrase "unapplied cash" from every user-facing surface.

---

# The proposed feature set

## 1 — Every bank line has an owner

### The inbox is a filter, not a container

**A bank statement line is not a new object.** It is a Money Line that has reached
`landed`, sourced from the bank rather than asserted by us. The inbox is the saved
filter `state = landed AND explanation is absent`, exactly as the work model
defines every other queue as a filter rather than a list. Saying this explicitly is
load-bearing: the fastest way to acquire a seventh noun called the Bank Statement
is to build a screen for it, and the Pile already demonstrated the discipline that
prevents it — the Pile is a station, not a container, and the same is true here.

The inbox has **two symmetric halves**, which is the review's orphan-queue insight
expressed on one object rather than two:

| Half | The filter | What it means | Colour |
|---|---|---|---|
| **The bank knows something we don't** | landed, unexplained | Money moved and we cannot say why | **Red.** Nothing else is. |
| **We know something the bank doesn't** | expected, past its date | We predicted money that has not appeared | Amber, and it is the predictor's error term |

Both halves are work; only the first is red. The second half exists because a card
batch that funds T+2 and lands on day five is not an error on day three and is
absolutely an error on day nine, and a product that cannot tell those apart will
either cry wolf or miss a processor outage.

### The ownership taxonomy

Twelve kinds, plus `unknown`. **A bank line is explained when exactly one
explanation object owns it.** The explanation object may itself contain many things
— one wire funds six remittances, one deposit slip carries eleven checks — which is
how "explained by exactly one thing" stays true without lying about the world.

| # | Kind | Direction | What explains it | Where it comes from |
|---|---|---|---|---|
| 1 | **Payer wire** | in | One electronic remittance's check-amount total, reassociated on the trace number | The clearinghouse connector |
| 2 | **Merchant settlement** | in | One processor batch, gross or net, from the settlement report | The processor connector or its report file |
| 3 | **Deposit slip** | in | One slip the product printed, listing its checks and cash | The daily balanced day |
| 4 | **Online payment batch** | in | The pay-link's own settlement — a Money Line whose payer is a patient who was never here | The processor, same rail as #2 |
| 5 | **Virtual card** | in | A payer-issued single-use card keyed at the terminal; arrives on the merchant rail, not the payer rail | The Pile, then the processor |
| 6 | **Named non-patient receipt** | in | Interest, a vaccine rebate, a records-request fee, a returned refund check, a class-settlement check, an owner transfer in | A named list, each with a destination account |
| 7 | **Old-system collection** | in | Legacy accounts receivable collected in the legacy system, landing in the same bank account | The old-system balance thread |
| 8 | **Refund disbursement** | out | One issued refund clearing | The refund register |
| 9 | **Merchant fee debit** | out | A monthly or per-batch fee under gross funding | The settlement report |
| 10 | **Chargeback or returned item** | out | A reversal against a prior settlement, or a bounced patient check, each usually paired with its own fee | The processor or the bank |
| 11 | **Payer debit** | out | A takeback the payer collects by debit rather than by offsetting a future check | The demand letter and the recoupment case |
| 12 | **Not ours to explain** | either | A line belonging to the practice's non-openPractice activity — rent, payroll, an owner draw — on an account that is not exclusively the product's | A disposition, with a reason, never a deletion |
| — | **`unknown`** | either | Nothing yet | Every line starts here |

Kind 12 needs defending, because it looks like an escape hatch and it is not. Many
small practices run patient receipts through an account that also pays the rent,
and a taxonomy with no honest home for the rent forces the staff to either lie
(classify it as something it is not) or give up on zero (leave permanent red).
Both destroy the inbox within a month. `Not ours to explain` is a dated,
attributed, reversible disposition with a reason, it appears on the close sheet as
a count, and it never enters the accounting export — which is precisely what makes
it safe.

Kind 7 is the one most likely to be missed and the one that would do the most
damage. FD-9 runs the legacy accounts receivable off in the legacy system, and
that run-off **lands in the same bank account**. If the taxonomy has no owner for
those deposits, every legacy collection is permanently red, the inbox never
reaches zero, and the staff learn within a fortnight that red does not mean
anything. Giving it a kind also produces a number the practice cannot otherwise
compute: a legacy deposit decrements the old-system balance thread's dollars, which
is how the money page's "down $1,880 this month" is *derived* rather than typed.

### The rules that keep it honest

- **Nothing auto-explains except an exact identifier match.** A trace number that
  matches, a settlement batch id that matches, a slip total and date that match one
  slip and only one. Everything else is a two-click human confirmation. At ten to
  thirty remittances a week a human clicking two rows together takes two seconds
  and carries zero false-match risk, and tuning a threshold takes longer than the
  practice will ever spend matching.
- **An inferred match is labelled inferred.** Amount-and-date matching and the
  subset-sum candidate (one wire funding several remittances) are offered as
  suggestions, are recorded with their confidence, and are undoable at tier 1 for
  thirty days. A wrong subset that happens to tie is the worst error available here
  because it is invisible; the label and the undo window are what make offering it
  responsible.
- **An unexplained line ages against a shipped dwell, not a configured one.** It
  appears on Today the day it arrives, on the close sheet that evening as a count,
  and on the owner's weekly money page by name once it is over a week old — which
  is where "Wed Aug 26, DEPOSIT — MOBILE, $186.00, no batch and no remittance
  matches it, Dana asks the bank Monday" comes from.
- **Explained is the export's precondition.** This is the valve, in the FR-3 sense:
  the verdict `explained` is consumed by the accounting export, which may contain
  only explained lines. That single rule is what makes the reconciliation
  certificate provable by construction instead of a claim, and it is why the inbox
  is a finishable task with a consequence rather than a hygiene screen.

## 2 — The check that ties: reassociation and the payer-level adjustment ledger

### Money and its explanation arrive separately

The money is in the bank Tuesday and the remittance arrives Thursday from a
different system. The product's job is not to guess: it is to **hold both until
they meet, and never post an explanation as if it were money.**

Matching runs on the payer's own reassociation trace number against the transfer's
addenda record, with the payer identifier as the secondary key; amount-and-date is
the labelled fallback. Three cases must be named rather than absorbed:

| Case | Behaviour |
|---|---|
| **Remittance with no money** | Posts to service lines and to patient balances; the visit thread reaches `remitted`, **not** `banked`. The expected Money Line carries the payer's observed funding lag as its date. |
| **Money with no remittance** | Explained provisionally as "a wire from this payer" and held. It is not red — it is amber with a name — because a payer wire whose remittance is two days behind is normal. |
| **Neither, past the window** | Work. And where the cause is that the practice is not enrolled for electronic remittance delivery with that payer, the product says so and produces the enrollment task rather than accumulating orphans forever. |

Two dangerous cases never auto-post under any confidence: a **duplicate**
remittance, and a **corrected or reversing** remittance carrying negative
adjustment amounts against already-posted lines. Both are recognised by name and
both queue.

### The payer-level adjustment ledger

This is the mechanism whose absence guarantees an unexplainable deposit. The
remittance's claim payments and the check amount differ by payer-level
adjustments, and until those are modelled, **every affected deposit fails to
reassociate for a reason nobody in the system can see.** The identity the ledger
maintains is simple and is checked on every remittance:

> check amount = sum of claim payments + sum of payer-level adjustment lines

A payer-level line that does not balance the remittance is itself work, and a
reason code the product does not recognise is work owned by the biller rather than
silently absorbed.

**The minting rule: a payer-level line becomes a thread only when it owes an
outcome.** This follows directly from the Thread contract's own minting rule —
`intent` means an outcome is owed — and it is what stops the ledger from
manufacturing dozens of cases nobody works.

| Line | Owes an outcome? | What it becomes |
|---|---|---|
| **`WO` — takeback / overpayment recovery** | **Yes.** Was it right, do we appeal, which visit, and did the offset actually land? | A **recoupment case thread** (see §3) |
| **`FB` — forwarding balance** | **Conditionally.** Only if it does not come back. | An **expected Money Line** dated to the payer's next cycle, which becomes work if the next remittance does not carry it |
| **`L6` — interest on a late clean claim** | No. It is an explanation, not an obligation. | A **named non-patient receipt** posting to interest income — *other* income, not patient service revenue, which matters at tax time — and **excluded from variance**, so it never reads as an overpayment |
| **`CS` — capitation** | No | A named line with its own account; the visit lines it accompanies pay zero legitimately and must not create variance |
| **Early-payment allowance, provider adjustments** | No | Named lines with destination accounts |
| **A reason code we do not recognise** | **Yes** | An unmatched item at `intent`, owned by the biller, because an unnamed adjustment is the definition of unexplained money |

Two consequences worth stating. A forwarding balance makes each remittance
individually unbalanced *by design*, so the balance check must be evaluated across
the pair rather than on one file, or the product will report a defect every time
the payer behaves correctly. And interest is the reason the variance work must
suppress known-benign classes **by name rather than by tolerance** — a tolerance
band wide enough to swallow interest is wide enough to hide a three percent
haircut.

## 3 — A takeback is a case, not a variance

A `WO` line reducing this week's check against a claim from last November is not a
variance on any claim in this week's file. Treated as one, it produces a false
underpayment on an unrelated visit and an unexplainable deposit at the same time.
It is a **recoupment case** — an enumerated non-visit thread, already defined in
the Thread contract, and this document is where its money half lands.

The arc, and where each part lives:

| Step | Object | Note |
|---|---|---|
| The demand letter arrives | A page in the Pile, dispositioned into a recoupment case at `intent` | Gilbert Ash's Medicare letter, $212.40, decide by Sep 24, Marcy |
| We decide | Appeal, or agree | Appealing applies `disputed`, which suppresses the patient's statement on the linked visit and starts the appeal's own clock |
| We elect | Refund by check, or let them offset | **Tier 4** either way: electing an offset commits practice money as surely as writing a check does, it is merely deferred |
| The offset lands | A `WO` line on a future remittance, which resolves the case at `banked` | This is the line that closes the loop |
| Or the refund clears | A bank debit from the refund register | Same ending, different rail |

Two rules make this work rather than merely describing it.

**The letter and the offset are the same case at two stations.** When a `WO` line
arrives and a recoupment case already exists for that claim, it resolves that case
— it does not mint a second one. When a `WO` arrives with no letter behind it, it
mints the case at `remitted` and *the missing letter becomes the case's first
question*, because a payer that recoups without notice is a fact the practice needs
in writing.

**The takeback carries the deadline that the appeal needs.** The review named this
seam precisely: a recoupment against a two-year-old claim can arrive after the
appeal window has already closed, the filing clocks live with claims, the takeback
lives with financial control, and nothing connects them — so the practice learns
about the lost window after it has lost it. The case's clock is the *nearer* of the
letter's decide-by date and the payer's appeal deadline derived from the original
date of service, and where that second number is already zero the case says so on
the day it opens rather than after the work is done.

The artifact is the **recoupment case file**: the demand letter, the original
claim as submitted, its remittance, the appeal if any, the election with its
tier-4 approval, and the bank line or remittance line where the money actually
moved. One click, because the practice will otherwise assemble it from four
places at the exact moment it is least able to.

## 4 — Gross is not net: merchant settlement, fees, chargebacks, and the virtual card

Card processing is a two to three percent tax on every patient dollar, and the
owner **never sees it on his bank statement** — it comes out before the money
arrives. The money page prints that fact deliberately: "$2,013.75 charged, $60.98
kept by the processor, 3.0% ... you never see the $60.98 on the bank statement; it
comes out before the money arrives. At this rate it is about $3,171 a year."
Nothing else in the packet tells a small practice that number.

### The funding model is observed, not configured

Processors fund two ways: **gross** (the deposit equals the receipts and the fees
are debited separately, usually monthly) or **net** (the deposit equals receipts
minus that batch's fees). Receipts posted at gross can never tie to a net-funded
deposit, and the difference is an expense rather than an error.

Do not ask the practice which model their processor uses — they will not know, and
FR-8 says to read it from an artifact instead. **Compare the first settlement's
batch total to its funding amount.** If they agree, it is gross and a fee debit is
coming; if they differ, it is net and the difference is that batch's fee. One
observation replaces a setup field, and it self-corrects if the processor changes
its terms.

### Everything the merchant rail produces, with a destination

| Thing | Direction | Destination | Thread? | Clock? |
|---|---|---|---|---|
| Batch funding | in | Bank; receipts already posted | no | funding lag, as an expected Money Line |
| Per-batch fee (net funding) | implicit | Merchant fee expense, as a line on that deposit's journal entry | no | — |
| Monthly fee debit (gross funding) | out | Merchant fee expense | no | — |
| Reserve hold | withheld | An expected Money Line with a release date | no | the release date |
| **Chargeback** | out | Reverses the receipt; reopens the patient's balance | **yes** | the representment deadline |
| **Chargeback fee** | out | Its own expense account, separate from the fee above | no | — |
| Returned patient check | out | Reverses the deposit; reopens the balance | no | — |
| Bank returned-item fee | out | Bank charges expense | no | — |
| Card refund to original method | out | Appears as a debit on the settlement, not as a check | no | — |

The chargeback is the only one that mints a thread, and for the same reason as a
takeback: an outcome is owed. Represent or accept, by a date the processor sets and
will not extend. Its dollars are outbound, its counterparty is the patient's bank,
its owner is the manager, and while it is open it applies `disputed` to the visit
thread — which suppresses the statement and stops the collections cadence, the
valve the review found had no consumer. Representing is **tier 3**, because
evidence leaves the building; accepting is **tier 2**. Its evidence packet is the
receipt, the stored-card consent with its version, the signature, and the visit
thread's story — assembled in one click, because a chargeback answered late is
simply lost.

A card refund is worth calling out separately: a refund of a card payment should
usually be a **card refund to the original method**, not a check. It is a merchant
operation with its own fee behaviour, it clears faster, and it cannot become a
stale-dated check sitting in a drawer for eight months. The refund register (§6)
therefore picks the rail from how the money arrived rather than defaulting to
paper.

### The virtual card is a payer-imposed fee, and nobody tells the practice

A growing share of payers pay by faxing a single-use credit card number that the
practice keys into its own terminal. The money then arrives as a **merchant
settlement**, net of two to three percent — which means the payer has converted its
contractual obligation into a payment that costs the practice a discount to
receive. It is a genuinely infuriating small-practice problem and it is entirely
absent from the packet's model.

The handling falls out of machinery that already exists. The fax lands in the Pile
and becomes a thread like every other page. Its disposition is "keyed at the
terminal on Aug 26 for $412.80." The remittance for those claims, when it arrives,
reassociates to a **merchant settlement** line rather than to a wire — kind 5 in
the taxonomy exists precisely so the reassociation does not go looking for a wire
that will never exist. And the haircut is measured per payer and named once a year
in one sentence: *"Cigna paid you by virtual card 41 times this year; it cost you
$1,180 to be paid. They will pay by transfer if you ask."* That sentence is the
artifact, it costs one query, and the practice can act on it.

## 5 — Paper is a channel, not an exception

Paper remittances and paper checks remain a real share of small-practice cash. The
review does not put a number on it beyond "a meaningful share," and this document
declines to invent one (see Open questions) — but the design consequence does not
depend on the size of the share. **Whatever does not come through this path
bypasses the entire money chain**, and every metric built on the chain is wrong by
however much that is, silently.

The path, and what each step produces:

1. **The envelope lands in the Pile.** The explanation of benefits and its check
   are pages like any other, so they arrive with a station, an owner, a clock, and
   a disposition from the moment they hit the desk.
2. **Keyed to the same shape as an electronic remittance.** Claim, service line,
   allowed, paid, adjustment reason, patient responsibility, and the check's total
   — with the same balance check as §2, so a paper remittance that does not tie is
   caught at the desk instead of at the bank three weeks later. The image is
   retained with a hash, because provenance that cannot produce the original
   document is a claim rather than evidence.
3. **Posted through exactly the same objects.** The same service-line posting, the
   same variance evaluation, the same patient balance, the same thread transition
   to `remitted`. There is no parallel paper ledger; that is the whole point.
4. **The check goes on the deposit slip the product prints.** Along with the
   drawer's cash and the day's other checks.
5. **The slip explains the bank line.** Because openPractice defined the deposit,
   the deposit matches by construction, and the reconciliation problem for cash and
   checks simply does not arise.

The Humana paper check of $743.28 on the money page sits inside "checks," not
inside "insurance," because that is where it appears to the owner in his own bank
app. The product should render money the way the bank renders it and explain the
difference underneath.

**One consequence nobody has stated, and it matters.** Paper lines are keyed by a
human, and FD-2 puts the learned baseline — a median over posted remittances — on
the critical path as the practice's expectation source. A mistyped allowed amount
therefore does not merely mis-post one claim; it **poisons promise memory** for
that payer and code, quietly, in the direction of whatever was typed. Every
manually keyed line must carry a provenance flag, and the baseline must either
exclude flagged lines or weight them down. This is cheap now and effectively
unfixable once there is a year of medians resting on it. Optical extraction is
deferred for the same reason inverted: a wrong number read confidently by a
machine is worse than a wrong number typed by a person who knows they might be
wrong.

## 6 — Money leaving the practice: refunds, held money, and their clocks

### Held money, by cause, in days remaining

"Unapplied cash" never appears on a surface. The page says **"money in, we don't
know whose."** Underneath it is three different things that resolve three
different ways, and collapsing them is why the single bucket grows forever:

| Cause | Whose it is | The clock | Resolution |
|---|---|---|---|
| **Unidentified receipt** | We do not know | The state's escheatment date | Identify, or eventually escheat |
| **Patient credit** | The patient's | State unclaimed-property rules | Refund, transfer within the same guarantor, or apply forward with a standing election |
| **Payer overpayment** | The payer's | The federal sixty-day identify-and-report obligation on federal-program money | Refund, or elect offset — which becomes §3's case |

Every one shows **days remaining, not days elapsed**. "Medicare overpayment: 41
days left to report" is actionable; "aged 19 days" is a fact about the past. And
each carries a **cause classifier** that is derivable from the ledger rather than
typed — the patient paid an estimate that exceeded actual responsibility, the payer
paid twice, primary and secondary both paid, the patient paid after insurance
already had.

The strongest thing available here is preventive rather than procedural: money
taken at the desk must carry its **allocation intent at capture time** — which
charge, which estimate, which plan instalment — so that held money can only
originate from money that arrived without us. A mailed check, a mobile deposit, a
payer's overpayment. Deferred allocation is how unidentified cash accumulates and
how reconciliation dies, and closing that door at the point of capture is worth
more than any worklist that processes the results.

### The refund disbursement register

Approval was already modelled in the packet; **issuance was not.** A refund is a
liability with a life after the signature, and every step of that life can fail.

| Station | What is true | Tier |
|---|---|---|
| **Approved** | The owner has said yes to a specific amount, to a specific party, for a stated cause | **4 — hard stop.** Money leaving the practice. |
| **Issued** | A check is printed and mailed, an electronic transfer is sent, or a card refund is submitted to the processor | 3 — it left the building |
| **Cleared** | A bank debit proves it | 2 — recorded |
| **Closed** | Nothing is owed | — |

And the failure paths, each of which is a real event with a real bank consequence:

- **Issued and never cleared.** A refund check nobody deposited. Past its dwell it
  becomes work; past the bank's stale-date window it must be voided and re-issued
  or escheated. A refund that was approved, issued, and never cleared is money the
  practice still owes while its ledger says otherwise.
- **Returned refund check.** Comes back as a bank *credit* — a named non-patient
  receipt, kind 6 — and it **reopens** the credit-balance thread it was meant to
  close. A `closed → remitted` reopen, which the Thread contract explicitly
  permits, dated and appended rather than edited.
- **Refund to a payer.** Procedurally different from a refund to a patient: it
  needs the claim reference and the payer's own form, and it carries the
  refund-versus-offset election. If offset is elected there is **no bank debit at
  all** — the money leaves as a future takeback, which is §3 from the other end.
  The refund register and the payer-level adjustment ledger are the same story told
  from opposite directions, and they must reconcile to the same dollar.
- **Refund of a card payment.** Goes back to the original method, appears on the
  settlement rather than as a check, and never becomes stale-dated.

**A tier-4 gate cannot auto-release, and this is a real asymmetry.** FR-6 requires
every gate to carry a deadline escape, and states it as auto-release at tier 3 for
a gate that would otherwise destroy the money it protects. That escape is correct
for a claim batch and wrong for a refund: releasing a refund without a human is
exactly the autonomous money movement the constitution forbids, so the sixty-day
clock cannot open the gate. **The escape at tier 4 is escalation, not release** —
the refund moves up the owner's weekly page, then onto Today by name, and the
clock's expiry is recorded as a fact rather than as a permission. At two to four
staff the approver is the owner and he is with patients, so the practical design is
to batch refund approvals into the weekly money page's "three things only you can
decide" and let escalation, not automation, carry the deadline. This asymmetry
should be written back into FR-6 as a stated exception rather than left as an
apparent contradiction.

## 7 — Tomorrow's deposit, before it arrives

**"Expected Monday Aug 31: $2,142.27 — Thursday's card money $534.07 plus
Ambetter's wire $1,608.20, whose remittance is here and whose money is not yet."**

That is the whole feature, and its two properties are what make it worth building.

**It is arithmetic, not a forecast.** It is the sum of expected Money Lines —
each one a known event with a known amount whose funding date has not arrived —
and it names its components. It is not a model, it has no confidence interval, and
when it is wrong it is wrong for a reason the practice can read: a batch that did
not fund, a wire that slipped, a payer that changed its cycle. Practices reconcile
far more willingly when the product predicted the deposit than when it merely
audits one afterwards, and the difference between a number the owner trusts and one
he does not is exactly this: whether he can see what it is made of.

**It scores itself every morning.** Yesterday's prediction meets today's bank, and
the difference is the amber half of the inbox — a predicted line that did not land.
The predictor is therefore not a separate report but the inbox's other side, which
is why it costs almost nothing to add once the inbox exists, and why it degrades
honestly: a payer whose cycle has drifted produces a visibly wrong prediction
before it produces a collections problem.

Its inputs are all observed rather than configured, per FR-8. The card batch's
funding lag comes from the first few settlements. The payer's transfer lag comes
from its own history — the median days between a funded remittance and its wire —
which is the same promise-memory machinery FD-2 already builds for allowed amounts,
pointed at a different question. Nobody types "BCBS pays on Mondays."

## 8 — The day balances, and the month is only printing

### The daily balanced day

Five minutes, inside the close, at 5:20pm when the terminal batch shuts. It is the
practice's actual control point and it replaces the monthly ceremony FD-3 deleted.

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

Three design points are carried by that block. The difference is **printed
unresolved and then resolved**, on purpose, because a sheet that only ever shows
zero proves nothing; this one proves the check ran. Cash float and over/short have
no home anywhere else in the model and this is it — cash over/short is the small
practice's most common integrity failure and no incumbent at this price point
watches it. And the deposit slip is generated rather than transcribed, which is
what makes tomorrow's bank line explainable by construction.

An unbalanced day does not block anything. It closes with the difference named, an
owner, and a date — and it appears on tomorrow's huddle page, which is the same
treatment an unclosed day gets.

### Landed is not banked

Here the two governing documents disagree, and the disagreement is worth fixing
rather than papering over. Feature Normalization's C-3 defines `landed` as
"externally verifiable: a line on the bank statement, **a check in hand**, a
counted drawer, a settlement report." The Thread contract defines `banked` as
"money moved and **a bank line proves it**." Read together, a check in hand would
advance a thread to `banked` on Tuesday and the bank would not agree until
Thursday.

The Thread contract governs thread mechanics, so it wins on the station: **a check
in hand does not bank a thread.** The reconciliation is to say plainly that the
Money Line's `landed` state carries a **source** — drawer, check in hand,
settlement report, bank statement — and that only the bank-statement source
advances a thread to `banked` and permits the line into an export batch. The other
sources are landed-but-not-yet-banked, which is exactly the gap the deposit slip
exists to close, and which the money page already prints as "still on its way:
Friday's drawer $137.00 and checks $95.00 go Monday." This is recorded as a
discrepancy below rather than resolved unilaterally.

### The export, and the only lock in the product

One journal entry per bank deposit, dated on the deposit date, split by income
account, with merchant fees as an expense line and refunds as their own line. The
entry total equals the bank line, so the bookkeeper's bank feed matches it in one
click — that single design choice is the difference between an export that is used
and one that is abandoned. Cash basis by default, accrual opt-in, at most eight
mapped accounts presented in plain language and mapped to the bookkeeper's own
existing accounts from a dropdown.

**Export-batch freeze is the only lock (FD-3).** Once a batch is exported it is
versioned and immutable; later corrections land in the next batch with
back-references to what they correct. The period itself never closes, a late
remittance never meets an obstacle, and the accountant still gets the stable
artifact he needs. A batch may contain only explained bank lines, which is the
valve from §1 doing its work.

The batch ships with a **one-page reconciliation certificate**, and it states its
exclusions rather than only its totals, because a certificate that cannot be wrong
is a certificate nobody checks:

> For July: collections $X, refunds $Y, merchant and bank fees $Z, net to bank $W.
> The bank shows $W. Difference $0. Not included: $412.00 received and not yet
> attributed to anyone; 2 lines classified as not ours to explain; the old system's
> collections of $1,880.00, which are cash to the practice and are not this
> system's revenue.

### Whole-practice truth

The owner's money page shows what is stuck in this system, then the legacy
run-off, then the sum — "in this system $17,618.40; still in MediTouch, the old
system, 214 items, oldest 411 days, $14,206.00, Marcy works it Thursdays, down
$1,880 this month; everything we are owed $31,824.40." The row exists so the total
is the truth rather than a flattering subset, and this document is what makes the
"down $1,880" arithmetic: legacy collections arrive as kind 7 in the bank line
inbox and decrement the old-system balance thread without moving its station,
which closes when its dollars reach zero.

---

## The rhythm, the tiers, and the artifacts

**Rhythm (FR-5).** Day: the balanced day at 5:20pm, the deposit slip, the close
sheet's money section, and today's unexplained bank lines. Week: the owner's
ten-minute money page, unexplained lines older than seven days by name, refund
approvals batched into "three things only you can decide." Month: the export batch,
the reconciliation certificate, the merchant statement's fee true-up. Quarter: the
escheatment review and the stale refund sweep. Year: the merchant-fee and
virtual-card totals, and payer interest separated for tax treatment as other
income.

**Clearing conditions.** Every work type here clears on a state change rather than
on a person marking it done: a bank line clears when it has an explanation; an
expected line clears when it lands or is written off with a reason; a recoupment
case clears when the offset lands or the refund clears; a refund clears when a bank
debit proves it; held money clears when it is attributed, refunded, or escheated.
Nothing on this list can be cleared by declaring it clear, which is what stops the
aging numbers from becoming a lie within a month.

**Governance tiers (FR-6).**

| Tier | Actions here |
|---|---|
| **1 — do + undo** | Matching a line to a settlement; classifying a fee; unmatching within thirty days; printing a deposit slip |
| **2 — do + notify** | Auto-explaining on an exact trace-number match; posting a remittance to service lines; accepting a chargeback; recording a cleared refund |
| **3 — delay + cancel window** | Representing a chargeback; releasing a refund batch to print; the accounting export batch |
| **4 — hard stop** | Approving any refund; electing a payer offset; writing off an unexplainable bank line as a loss |

**Artifacts (FR-4), each with the person who touches it.** The deposit slip — the
person going to the bank. The reconciliation certificate — the bookkeeper, monthly,
and it is his entire experience of the product. The owner's money page — the owner,
ten minutes a week. The close sheet's money section — the office manager, nightly,
accumulating into the binder one day at a time. The recoupment case file — the
biller, on a demand letter. The chargeback evidence packet — the manager, against a
deadline. The one-sentence virtual-card fact — the owner, once a year, before he
renews. Undo — anyone, weekly.

**Learning (FR-8), for every field this document would otherwise ask someone to
type.** The account list comes from the bank's own export. The funding model comes
from comparing the first settlement's batch total to its funding. The fee structure
comes from the settlement report. The payer's funding lag comes from its own
history. The chart-of-accounts mapping comes from the bookkeeper's existing
QuickBooks account list. What genuinely cannot be read from an artifact is the
state's escheatment schedule, and that is the honest exception — it is authored,
and the document says so rather than pretending otherwise.

## Interfaces and boundaries

**Consumes.** Bank statement lines, as an uploaded file for the beta. Processor
settlement reports and their batch, fee, chargeback, and reserve detail.
Electronic remittances from the clearinghouse connector, with their check totals,
trace numbers, and payer-level adjustment lines. Scanned explanations of benefits,
paper checks, demand letters, and virtual-card faxes from the Pile. The drawer
count, the terminal batch, and the day's posted receipts from the close. Threads,
for attribution and for the `banked` transition. Promises, for observed payer
cycles and the observed funding model. Roster entries, for every owner and every
counted drawer.

**Emits.** Money Lines in three states with a source and an explanation link. The
`banked` transition on threads, and only where a bank line proves it. Recoupment
cases, chargeback cases, and credit-balance threads with their clocks. The deposit
slip. The export batch with its back-references and the reconciliation certificate.
Work, in both halves of the inbox. The predicted deposit and its error. The
decrement on the old-system balance thread.

**Boundaries, stated as prohibitions.** openPractice never originates a payment,
never holds credentials that could move money, and reads the bank only as a file.
It does not own the general ledger, the bank account, taxes, payroll, vendor
payables, or the financial statements. It does not model the practice's
non-openPractice spending beyond the ability to mark a line as not its business.
The processor owns the card rails and the PCI scope, which stays out of the product
by using the processor's hosted page and a webhook. The legacy system remains
authoritative for legacy accounts receivable; this document only explains the bank
lines that run-off produces and never re-derives the balance. And the ownership
seam holds in the other direction too: the connector owns transport and partner
protocol behaviour, this document owns what the money *means*, and the reason
taxonomy — defined in Slice 0, before anything posts, because it is the join key to
the general ledger and is unfixable downstream — is consumed here rather than
extended.

## Alternatives and tensions

**The bank line as the unit, versus the batch.** Choosing the bank line makes the
work finishable and gives the owner something he can verify in his own banking app,
which is the entire differentiator. The cost is a real dependency: the practice must
be able to produce a statement file, and a bank that exports nothing is a risk this
document cannot mitigate by design. The fallback — reconcile only to the
processor's and the payers' own reports and let QuickBooks' bank feed do the bank
match — eliminates the import work and is genuinely weaker, because the product can
then no longer prove that it ties, and "does it tie" is one of the owner's three
questions.

**A statement upload, versus a bank feed.** An aggregator would remove the monthly
upload and add a vendor, a contract, credentials, an outage mode, and a data-sharing
question. For a first beta at one practice, a monthly file is the right trade and
the upload itself is a five-minute task. It is worth being honest that this makes
the inbox monthly rather than daily unless the practice uploads more often, which
weakens "the owner opens his bank app on Friday" until it does not.

**Automatic matching, versus two clicks.** Auto-matching on an exact trace number
is free and safe. Everything beyond that trades a false-match risk for time the
practice does not actually spend: at ten to thirty remittances a week, a human
clicking two rows together is faster than tuning a threshold and carries no risk of
a silent wrong answer. The subset-sum suggestion is the one exception worth taking,
because payers routinely fund several remittances in one transfer and the manual
version of that is genuinely tedious — but a wrong subset that happens to balance is
the worst error in this document, which is why it is labelled, recorded with a
confidence, and undoable for thirty days.

**Explained by exactly one thing, versus one-to-many.** "Exactly one" is what makes
the definition of done visible, and reality routinely funds six remittances with one
wire. The resolution — one bank line owned by exactly one *explanation object*,
which may itself contain many members — keeps the promise honest, but it does move
the complexity one level down rather than removing it, and the explanation object is
where a future design will feel the strain.

**Minting threads from payer-level adjustment lines.** Minting one per line would
produce a flood of cases nobody works and would make the aging numbers meaningless.
Minting none would lose the takeback, which is the most confusing thing a
small-practice biller encounters. The rule taken — *mint only where an outcome is
owed* — is the right cut, and it will be argued at the margins, particularly for
forwarding balances, which owe an outcome only conditionally and are modelled here
as an expected Money Line rather than as a case.

**Paper keyed by a human, versus extraction.** Keying is slow and is the honest
choice for a first beta, because a confidently wrong extracted allowed amount
corrupts promise memory in a way that is invisible and compounding. The tension is
real either way: keying is also a place where a tired person at 4:50pm poisons the
same baseline, which is why the provenance flag on manually keyed lines is not
optional in either design.

**Tier 4 on every refund, at four staff.** The hard stop is constitutionally
required and it will be felt, because the approver is the owner and the owner is
with patients. Batching approvals weekly is the mitigation; the residual risk is
that a sixty-day federal clock and a weekly approval rhythm can collide, and the
answer must be escalation rather than automation even when escalation is slower.
A product that quietly auto-approves small refunds to keep the queue moving has
crossed the one line the constitution actually draws.

**The inbox's credibility is all-or-nothing.** Every design decision above is
downstream of a single fragility: if red ever means "probably fine," the inbox is
dead and no amount of correctness elsewhere recovers it. That is why the legacy
run-off gets a kind, why "not ours to explain" exists, and why an unfunded card
batch on day three is amber rather than red. The failure mode is not a wrong
number; it is a staff that has learned to scroll past a colour.

## Open questions

- **The paper share has no source.** The brief for this document cites 10–30% of
  small-practice cash arriving as paper explanations of benefits and checks; the
  review says only "a meaningful share," twice, and no document in the packet
  states a percentage. The figure should be sourced or dropped before it is
  repeated as a fact — and it is worth measuring at the beta practice directly,
  since the answer changes how much of Slice 4 the paper path deserves.
- **`landed` versus `banked`.** Feature Normalization's C-3 includes "a check in
  hand" and "a counted drawer" in `landed`; the Thread contract requires a bank
  line for `banked`. §8 proposes a `source` attribute on the Money Line as the
  reconciliation. This needs adjudicating in whichever document owns it, because
  every "does it tie" number depends on the answer.
- **A tier-4 gate's deadline escape.** FR-6 states that every gate carries a
  deadline escape and describes it as auto-release. Tier 4 cannot auto-release
  without violating the constitutional line the tier exists to protect. Is the
  escape at tier 4 escalation-only, and should FR-6 say so?
- **Does the beta import a bank statement at all?** FD-8 requires one live merchant
  settlement reconciled to a bank line, which requires a bank line — but not
  necessarily an import mechanism. A human reading his banking app and confirming
  one line would satisfy the letter of the criterion and none of its intent. Decide
  before Slice 4 is staged.
- **What can the design partner's bank actually export**, in what format, and how
  far back? This is the same class of unknown as R-4's remittance history and
  deserves the same treatment: ask early, and let the answer size the slice.
- **Where does a legacy collection post?** It is cash to the practice and it is not
  this system's patient-service revenue. Does it enter the export at all, and to
  which account? The bookkeeper should decide this, once, before the first batch.
- **A forwarding balance that never returns** — is that a payer follow-up thread, or
  does it stay an overdue expected Money Line with an owner? The second is cheaper
  and may not survive the first payer who does it habitually.
- **The auto-match confidence for a subset-sum candidate**, and whether an inferred
  match may ever reach an export batch without a human having touched it.
- **Should manually keyed remittance lines be excluded from the learned baseline,
  down-weighted, or merely flagged?** Exclusion is safest and, at a practice with a
  meaningful paper share, discards real evidence FD-2 is starved for.
- **Escheatment is state-specific and has no source artifact.** Does the beta ship
  one state's schedule, a generic clock the practice sets once, or a referral to
  counsel? This is the one place FR-8's learning rule has nothing to read.
- **Shared personal and practice accounts.** Is "not ours to explain" a sufficient
  disposition, and does the product need to say anything about the commingling it
  is quietly observing?
- **Does the reconciliation certificate get signed, and by whom** — the bookkeeper,
  the owner, or nobody? The close sheet is signed; the certificate currently is not,
  and the binder may want it to be.
- **Sales-tax-bearing items** — retail products, supplements, cosmetic services —
  exist at many small practices, have a tax treatment the packet never mentions, and
  would arrive on the merchant rail like everything else. Declared out of scope here
  rather than solved, but it should be declared somewhere.

## Relationships

Binds to [The Visit Thread](openpractice-small-practice-beta-visit-thread.md),
which governs every thread mechanic used here: the `banked` station and its
requirement of a bank line, the recoupment case and credit-balance non-visit types,
the `disputed` condition that suppresses a statement, the reopen from `closed` that
a returned refund check needs, and the re-association key set. Governed by the work
model and the eight-rule feature contract in
[Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md),
particularly the Money Line's three states (C-3), the retired vocabulary in FR-2,
the valve rule FR-3, the four tiers of FR-6, and the learning rule FR-8. Sequenced
as Slice 4, "the money ties," in
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md),
whose acceptance criterion for this capability is one live merchant settlement
reconciled to a bank line (FD-8) and whose close sheet is the integration test this
slice must add its section to.

Sibling Tier-1 atomics. The day frame, the close, and the huddle page this document
writes its money section into are owned by
[The Day And Its Rhythm](openpractice-small-practice-beta-day-rhythm.md). The desk
half of the money model — the drawer, the terminal batch, the deposit slip's first
contents, the estimate, and the Good Faith Estimate whose dispute suppresses a
statement — is owned by
[Self-Pay, Time-Of-Service, And The Good Faith Estimate](openpractice-small-practice-beta-self-pay-and-gfe.md).
The clocks a recoupment case borrows from the appeal window, and the corrected
claim a reversing remittance produces, are owned by
[Secondary And Corrected Claims And The Deadline Control Plane](openpractice-small-practice-beta-secondary-corrected-claims-and-deadlines.md).
The old-system balance thread whose dollars this document decrements, and the
progressive activation whose sixth "first" is a day banked, balanced, and closed
with a signature, are owned by
[Day One: Migration And Activation](openpractice-small-practice-beta-day-one-migration-and-activation.md).

Supersedes in substance the deposit, settlement, reassociation, credit-balance,
close, and export feats in
[Financial Control And Accounting Bridge](openpractice-small-practice-beta-financial-control-and-accounting-bridge.md),
which remains the source for the subledger's shape and the accounting boundary but
whose "controlled close and correction" feat is deleted by FD-3 and whose
deposit-and-settlement workbench is reframed here as a bank-line inbox. The
findings this document is built from are in
[Feat Review: Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md)
— F8, F9, F10, F11, F12, the twelve missing feats, and the "every bank line has an
owner" reframe — and the paper-remittance and recoupment gaps are from
[Feat Review: Revenue Cycle](../review/openpractice-feat-review-revenue-cycle.md)
CL-4 and its cluster-level missing feats. Ratified framing decisions FD-3
(always-closed books and export-batch freeze), FD-5 (the four tiers), FD-8 (one
live merchant settlement reconciled to a bank line), and FD-9 (the old-system
balance) are in
[Framing Decisions](../review/openpractice-framing-decisions.md). Every printed
figure quoted above is from Pages 2 and 3 of
[Paper Prototypes](../review/openpractice-paper-prototypes.md), which are the
visual acceptance target for this slice.
