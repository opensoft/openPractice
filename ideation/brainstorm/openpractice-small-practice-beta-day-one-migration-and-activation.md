# Day One: Migration Boundary, Opening Balances, And Progressive Activation — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should replace the cutover project with a one-sentence migration boundary — active patients, future appointments, and one summary thread per open balance cross; closed history and full AR detail do not — plus a signed opening reconciliation the owner attests to, import rejects that arrive as work with owners rather than as a report, a read-only remittance-history import that seeds promise memory behind a pre-openPractice provenance boundary and never posts, a watched legacy run-off rendered on the owner's money page, and six firsts that each activate on one real success; and it should say out loud that the boundary is one-way, that only two acts are irreversible — the owner's signature and the first accepted claim — and that there is no go-live weekend.
Topics: openpractice-small-practice-beta, day-one, migration, progressive-activation
Repository context: openPractice — the migration boundary, opening balances, and activation sequence for the first small ambulatory practice beta
Captured: 2026-08-27

## Possible feats

- **Open items only, and the boundary is one sentence** — what has a future crosses, what has only a past does not, stated in one line the owner can repeat to his staff without opening the product.
- **The boundary is one-way** — openPractice never bills, appeals, or rebills a legacy item, but every dollar the payers and patients send for legacy items lands here anyway the moment ERA, EFT, and the merchant account point at the new bank; the design must receive money it may not work.
- **The opening balance is attested, not imported** — one printable page of totals the owner signs against the aging report he already believes, with the grain of the threads equal to the grain of the report, so the two tie by construction rather than by arithmetic nobody checks.
- **Rejects are work with a name on them** — every row that did not cross becomes a thread with an owner, a clock, and a disposition, and an open balance reject is what blocks the signature; the import report is deleted.
- **Evidence behind the boundary** — prior EOB and ERA history imported read-only to seed the observed Promise, marked pre-openPractice everywhere it is used, never posting to the ledger, and the only import in the product that can be thrown away at tier 1 because it changed nothing.
- **Six firsts, six go-lives, computed not claimed** — each first is a query over threads rather than a checkbox, each turns off exactly one thing in the legacy system, and each names what is entered twice until it does.
- **Two irreversible acts, and rollback never deletes** — the owner's signature and the first accepted claim are the only lines that cannot be walked back; everything else re-points authority rather than erasing history, and after the first accepted claim the honest word is *correct*, not *roll back*.

## Focus

How does a practice that is already running — with open A/R, in-flight claims, next month's schedule, and patients halfway through payment plans — start using openPractice on an ordinary Tuesday, without a weekend, a control room, or a day on which it bets everything; and what exactly is true, attested, reversible, and irreversible at each step of that arrival?

## Current state: there is no implementation project; there is a first one of each

The [practice-administration review](../review/openpractice-feat-review-practice-administration.md)
graded six implementation feats and kept almost none of them. The cutover control
room was "the wrong ceremony by two orders of magnitude" — a three-person practice
goes live on a Monday after a light Friday, and building a cutover surface signals
that openPractice implementations are hard, which is the opposite of the intended
positioning. Configuration promotion across development, test, training, and
production was "fantasy infrastructure" for a practice that will have one site.
Policy simulation was deferred in favor of preview on real data. Versioned
configuration sets reduced to a change log and undo. FD-9 ratified the deletions
and named the replacement: **progressive activation, open-items-only migration,
legacy AR running off where it already lives, and a migrated-balance thread type
so the run-off is watched work rather than invisible work.**

What survived the review is a single reframing, and it is this document's charter:
**"There is no implementation project; there is a first one of each."**

Two findings from that same review are the reason this document exists separately
from the setup flow. First, **migration rehearsal survived as essential-core only
"if scoped brutally"** — for a small practice, migration is a demographics file, a
future-appointments file, and an A/R aging report, and *the A/R usually should not
migrate at all.* Second, and more pointedly, the guided-bootstrap feat's largest
gap was that it has **no handling of the practice that is already running and
switching mid-cycle — open A/R, in-flight claims, existing future appointments,
patients mid-payment-plan — "which is the real beta scenario and a different
problem from bootstrapping a greenfield practice."**

That gap is what this document owns. The existing
[Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md)
atomic owns the guided setup conversation, lazy discovery, and the change log; it
still carries the cutover, promotion, and simulation feats that FD-9 retired and
is due its own revision. This document owns **day one**: what crosses the boundary,
what the owner signs, what happens to the old system, when each lane turns on, and
what can and cannot be undone. Where the two touch, the seam is stated in
*Interfaces and boundaries* below rather than described twice.

One prior-art note that shapes everything below. The
[paper prototypes](../review/openpractice-paper-prototypes.md) put the run-off on
the owner's money page as a printed row — *"Still in MediTouch, the old system …
214 … 411 days … $14,206.00 · Marcy works it Thursdays. Down $1,880 this month"* —
directly above *"EVERYTHING WE ARE OWED $31,824.40"*. That row is the acceptance
target for most of this document. It is also the reason the migration cannot be a
quiet back-office exercise: the migrated balances are on the page the owner reads
every Friday, next to the number he judges his practice by.

## Proposed feature set

### The boundary, in one sentence — and it is one-way

The boundary is stated as two lines and printed on the setup one-pager, because a
boundary the owner cannot repeat is a boundary his staff will violate by Thursday:

> **What crosses:** the people we will see and the money we will chase — active
> patients, the coverage on file for them, every appointment after the read-only
> date, and one summary thread per open balance.
>
> **What does not cross:** everything with a past and no future — closed visits,
> posted transaction detail, adjustment and payment history, statement history,
> documents and charts, recurring appointment series, payment plans, and stored
> cards.

| Class | Crosses? | Grain and note |
|---|---|---|
| Active patients | **Yes** | Anyone with a future appointment or an open balance. Party register facts only (FR-8's substrate): name, DOB, contacts, guarantor, household. Definition of "active" is the practice's, not ours, and is written on the reconciliation sheet. |
| Coverage on file | **Yes** | As a declared Promise with its source dated *pre-openPractice*. It is a starting point for an eligibility check, never an answer. The first real 271 supersedes it. |
| Future appointments | **Yes** | Every appointment on or after the read-only date, minted as a Thread at `booked`, carrying provider, location, and offering. Readiness runs against them on arrival, so several will be red on day one — that is the point. |
| Open balances | **Yes, as summary threads** | One **Old-System Balance Thread** per row of the legacy aging report. Never as AR detail. |
| Remittance history | **Evidence only** | Read-only, behind the provenance boundary, never posts. See below. |
| Closed visits and posted history | **No** | It has no future. It stays in the legacy system, and the exit archive preserves it. |
| Documents, charts, scanned paper | **No** | Clinical paper passes through this practice; it does not live here. The legacy document store is part of the exit archive; the EHR keeps what is clinical. |
| Recurring series | **No** | Each future occurrence crosses as its own Thread with no series parent. A series is a saved filter plus a link type, not an object the beta ships. |
| Payment plans | **No — and this must be said out loud** | A plan is a Promise made through the old processor. It cannot cross. Each plan patient is a scripted desk or phone conversation and a new plan here, or the plan continues in the legacy system until it retires. |
| Stored cards and autopay | **No — structurally impossible** | Tokens belong to the old processor and are not portable. Every autopay patient re-authorizes on the new merchant account or reverts to the pay link. |

**The boundary is one-way, and this is the finding that most changes the design.**
openPractice never *sends* anything for a legacy item: no claim, no corrected
claim, no appeal, no statement. But the moment the practice enrolls for ERA and
EFT with a payer and points the merchant account at the new bank, **every dollar
those payers and patients send arrives here** — including dollars for claims that
were submitted from MediTouch and were never in openPractice at all. ERA delivery
and EFT are enrolled per payer and per bank account; they are not scoped by date
of service. So:

- a payer's 835 for a legacy claim arrives in the new mailbox;
- a legacy patient balance paid by card arrives on the new merchant settlement;
- both land on a bank line, and a landed line explained by nothing is red (D-4).

The consequence is unavoidable: **an Old-System Balance Thread must be able to
receive money, even though it may never be worked.** The visit-thread contract
currently reads that a migrated thread's "only advancing move is `closed`" — read
strictly, that prohibits `remitted → banked`, and read strictly it makes every
legacy dollar permanently unexplainable on the bank statement. The reading this
document proposes, and asks the contract to state explicitly:

> A migrated balance thread may **receive and explain money** — `remitted →
> banked → closed` — and may never be **claimed, rebilled, corrected, or
> appealed** from openPractice. Receiving is not working.

Two additive contract requests follow, both minor bumps under the versioning rules
(a new optional facet and a new link type are additive): migrated threads carry
the **re-association key set** (payer, member id, date of service, rendering
provider, billed amount, payer claim control number) wherever the legacy export
supplies it, so an inbound legacy 835 finds its thread instead of flooding the
Pile; and where it does not, the unmatched inbound thread gets a named
disposition — **"old-system claim: post it in MediTouch"** — which is a
thirty-second decision instead of a mystery.

### Opening balances: summary threads and a signed reconciliation

**The grain of the threads is the grain of the legacy aging report.** Not a
custom extract, not a per-service-line reconstruction: the report the practice
already prints, already reads, and already manages to. If the legacy system ages
by patient, the threads are per patient; if by claim, per claim. Two reasons.
The totals then tie *by construction* rather than by arithmetic nobody checks.
And the number the owner is asked to attest to is the number he has been
believing for years, which is the only number he can honestly sign.

Each row becomes an **Old-System Balance Thread**, minted at the station its
legacy state maps to — `claimed` where a claim is outstanding with a payer,
`remitted` where a balance sits with the patient — carrying dollars, an owner
(whoever runs it off; Marcy, Thursdays), a clock where the legacy filing or
statute date is known and empty where it is not, and a provenance stamp that
prints as one line: *"brought over from MediTouch on Sep 1, balance $340.00."*
There is no history behind that line, and the thread says so rather than implying
one.

**The signed opening reconciliation** is one printed page — FR-4 already names it
in the sanctioned artifact list — and it carries exactly this:

1. the as-of date and the exact legacy report it was taken from;
2. count and dollars per aging bucket **as the legacy system printed them**;
3. count and dollars **loaded**, per bucket;
4. the difference, which is zero or is explained line by line with a reason;
5. what deliberately did not cross, named by class, with its dollar figure where
   one exists (closed history, detail, plans, stored cards);
6. the definition of "active patient" this practice used;
7. the owner's signature and date.

What the signature attests to is deliberately modest and therefore signable:
**"these are the totals my old system printed on September 1, and this is what
came across."** It is not an assertion that the legacy balances are correct — the
owner does not believe that either, and asking him to certify it is how a
reconciliation goes unsigned for six weeks.

**Governance.** The dry run is free and repeatable. The commit is **tier 2**
(do + notify) because before signature the whole import is voidable as a set. The
**signature is tier 4** — the owner personally, no delegate. After signature, an
opening balance changes only by dated reversal plus an amended sheet, also tier 4.

**The valve (FR-3).** An unsigned or unreconciled opening balance **suppresses the
whole-practice owed total on the money page.** The owner sees the two subtotals
and a named blocker where "EVERYTHING WE ARE OWED $31,824.40" would be, because
that single number is the most consequential figure in the product and it is a lie
until somebody has attested to half of it. This is also what makes the signature
happen: it is not a compliance step, it is how the owner gets his number back.

**If the legacy system cannot export.** Risk R-4's sibling. Every practice
management system on earth can *print* an aging report. The fallback is to type
it — 214 rows is roughly two hours of front-desk time, and if even that is too
much, the threads are created at the bucket grain (six rows) and the detail stays
where it is being worked anyway. The reconciliation is unaffected: it ties to the
printed report either way, and the run-off row on the money page is correct at
either grain. **The migration is never blocked on a vendor's export cooperation**,
which matters because the outgoing vendor has no reason to cooperate.

### Rejects are work with a name on them

An import produces a dry run and then a commit, and the dry run's output is not a
report. **Every row that did not cross becomes a Thread at `intent`** with a
plain-language reason, an owner, and a clock. There is no rejects CSV, no error
log, and no screen called "import results" — those are how sixty rejected rows
become forty duplicate patients in month two.

| Reject class | Default owner | Clock | Why that clock |
|---|---|---|---|
| Future appointment | Front desk | The appointment time | Someone will physically arrive. This is the only reject class that can hurt a patient on Tuesday. |
| Active patient | Front desk | That patient's next appointment | It becomes urgent exactly when they book. |
| Coverage row | Office manager | The next appointment it affects | An eligibility check supersedes it anyway. |
| Open balance | Office manager | The reconciliation signature date | An open balance reject **blocks the signature** — that is its valve. |

**Clearing condition (FR-5).** A reject clears one of two ways: the source row is
fixed and re-run, which clears it structurally; or it is dispositioned *"stays in
the old system"* with a reason, which is a human act but a consequential one —
it appears as a line on the reconciliation sheet and changes the dollars the
owner signs. There is no "mark done" that clears a reject invisibly.

Duplicates deserve a note, because import is where a practice's duplicate problem
is created for a decade. A row that matches an existing party **never
auto-creates** and never auto-merges; it parks as a reject with the
*distinguishing* fields shown, and "I'm not sure" is a legal disposition that
holds it rather than forcing a wrong choice. Merge and its thirty-day reversal
are owned elsewhere; the import's only obligation is never to guess.

### Evidence behind the boundary: the Remittance History Import

Operational migration and evidence import are different things, and conflating
them is how FD-9's scoping would have starved FD-2's cold start. **Remittance
History Import (Evidence Only)** is a read-only import of prior EOB and ERA
history whose sole purpose is to seed the observed Promise — the median allowed
by payer × code × modifier × place of service that the practice will otherwise
not possess until it has posted its own remits for months.

Its constraints are absolute and are the reason it is safe to run at all:

- it **posts nothing** — no ledger row, no Money Line, no AR, no patient balance;
- it **creates and advances no Thread**, and never links to one;
- it is **counted in no metric** — not in the Daily Five, not in the Monthly Six,
  not in collections, not in aging;
- every promise-memory row it produces carries `source = pre-openPractice` with
  its date range, and **every surface that renders such a Promise says so**:
  *"Aetna usually pays $103.40 for a 99214 — n=23, 18 of them from before
  September 1."*

Because it changes nothing, it is **the only import in the product with a free
undo**: deleting it and re-running it is tier 1, silent, and costs nothing. That
property is worth naming, because it also puts the evidence seed structurally
*outside* the append-only ledger and therefore outside the retention-versus-
immutability collision tracked as R-6.

**The seam is the first thing that will misfire.** Pool imported and live samples
naively and the first drift digest after go-live will report that every payer
changed its behavior on September 1 — because the sample era changed, not the
payer. Two mitigations, both cheap: the drift comparison requires a floor of
*live* n before it will fire for a payer × code, and imported lines are weighted
by recency so a 2024 sample cannot outvote last month. Until that floor is met,
the observed Promise is shown with its provenance and used for the desk
conversation and the estimate, but the alarm stays quiet.

**If the legacy system cannot export remittance history at all** (R-4, still
open), the honest consequence is stated rather than engineered around: D-2 is
demonstrable only late in the beta, promise memory activates progressively as the
practice's own n grows, the public Medicare fee schedule serves as a reference
band, and the beta **measures** when D-2 became real rather than asserting it did.

### The legacy read-only period and the run-off

**Two dates, not one.**

The **read-only date** is the day nothing new is created in the legacy system: no
new appointments, no new charges, no new claims. It is a named Tuesday, not a
weekend, and it is thrown per lane rather than all at once — that is what
progressive activation *is*, seen from the legacy side. The old system stays
licensed, readable, and workable for run-off: payments post there, denials are
appealed there, and legacy statements go out from there, because openPractice
does not hold the detail to produce them. That is a real, ongoing cost of
open-items-only and it belongs on the page, not in a footnote.

The **retirement date** is the day the license lapses. Between the two is the
run-off, and it is watched work.

**Rendered where the owner already looks.** The run-off is one row on the money
page — count, oldest age, dollars, who works it, and the month's delta — sitting
directly above the whole-practice total. The prototype's row is the target:
*214 · 411 days · $14,206.00 · Marcy works it Thursdays · down $1,880 this month.*
Its rhythm is FR-5's week (Marcy, Thursdays) and month (the delta and the
decision below).

**When the run-off ends.** Scope-and-sequencing leaves "who decides the run-off is
finished" open. This document proposes an answer the owner can act on, printed
monthly beside the row: **the run-off ends when what is left is worth less than
the license that holds it.**

> MediTouch costs $180/month. $14,206 left, coming down about $1,880/month.
> At this rate it pays for itself until March.

That comparison is arithmetic over facts the product already holds, it is legible
to a physician-owner in four seconds, and it converts a vague dread into a dated
decision. It does not decide for him.

**The exit archive.** Before the license lapses — and gated on it, at tier 4 —
the practice takes a complete print or export archive of the legacy system: AR
detail, transaction history, patient ledgers, documents, and the final aging
report. openPractice's obligation is small and firm: **record that the archive
exists, what it contains, its hash, where it lives, and who is custodian**, as
practice-level evidence with no patient subject. Whether it also *stores* the
bytes is an open question below. The review named the exit path as both a
retention question and a trust question; a product that helps a practice leave its
last vendor cleanly has said something credible about the next time.

**The closing reconciliation.** The run-off should end the way it started: one
signed page saying what remained, what was written off, what was handed to
collections, and what the archive holds. The opening sheet without a closing sheet
leaves the boundary permanently ajar.

### Day one starts ten weeks early: the kickoff threads

FD-8 retired the test-traffic escape, which puts two partner-paced dependencies on
the beta's critical path. Both open at kickoff — **before any software is
configured**, because they are the two things nobody can hurry:

1. **Clearinghouse enrollment.** Gates first #4.
2. **Merchant enrollment.** Gates first #6's live half.
3. **Per-payer ERA and EFT enrollment** — named here because FD-8 says "two
   threads" and the reality is two plus one per payer. 835 delivery and EFT are
   enrolled with each payer separately, take thirty to sixty days each, and are
   the actual gate on first #5. A practice with eight payers has ten enrollment
   threads, not two. This is a genuine amplifier on risk R-5 and should be said
   before someone discovers it in week six.

Each is a thread with an owner (the office manager), a station path
`intent → claimed` (submitted) `→ remitted` (they answered with an effective
date) `→ closed`, and a clock that is **the beta's own need-by date**, since no
payer set one. Each carries its BAA as a fact, because **BAA-Gated Vendor
Activation** means no connector activates without a linked, dated, unexpired one.

**Their valve.** While a kickoff thread is open, the first it gates is not an
error and not a red X. The practice page says *"Not yet — waiting on the
clearinghouse since Aug 12. Dana called Tuesday; they want the voided check
again."* A partner-paced dependency rendered as a product failure teaches staff
to distrust the product for something it did not do.

**A contract question this raises.** The Thread contract's non-visit types are a
closed enumeration of eight, and its **enrollment case** is defined as a provider
× payer × location approval. A clearinghouse or merchant enrollment is neither a
provider nor a payer. Two additive resolutions exist — widen the enrollment case's
counterparty to *any outside party whose approval gates money*, or add a ninth
type — and both are minor bumps. It must be **decided in the contract** rather
than assumed by this document; recorded in *Open questions*. There is also a real
strain either way: the contract says a gating thread's dollars and clock derive
from what it gates, and at kickoff these two gate everything and hold nothing, so
their dollars are zero and only the clock can sort them.

### First run is a confirmation, not a configuration

FR-8 governs: the practice's own artifacts are the primary configuration source
and hand authoring is the fallback that must be justified. Day one's contribution
is narrow — it names only what must be confirmed **before a first can be
attempted**, and leaves the flow itself to the implementation atomic.

Four artifacts on the desk plus one public lookup answer nearly all of it: the
**W-9** (legal name, tax ID, address), **one prior CMS-1500 or 837** (group NPI,
pay-to, billing provider, place of service, payer identifiers, rendering
providers), **one remittance** (the payer list the practice is actually paid by,
and the payer identifiers as the payers themselves write them), the **staff
roster**, and **NPPES** by NPI (organization name, taxonomy, authorized official,
addresses). Every field arrives pre-filled and says where it came from: *"Your
last claim says the pay-to address is 412 Cedar St, Suite 3. Yes / Fix."* The
office manager confirms; she never authors. The step emits a printable one-page
**"How your practice is set up"** she can hand to her accountant.

What gates which first — and nothing else is asked for on day one, because
everything else is requested at the moment it first blocks something, with the
blocking thing named:

| First | Minimum that must be confirmed first |
|---|---|
| #1 booked | Practice name, address, hours, locations, providers with NPIs, three appointment types |
| #2 eligibility | One eligibility route, payer identifiers, member-id format tolerance |
| #3 charge | The code sets (below) and a price per offering |
| #4 claim | Group NPI, tax ID, pay-to, rendering providers, clearinghouse credentials, submitter ID |
| #5 remittance | Per-payer ERA enrollment, the reason taxonomy |
| #6 banked day | Merchant account, bank account, deposit destination, at most eight mapped GL accounts |

### The code-set import

FD-11 and FR-7 make this a first-run step rather than a packaging problem. The
repository ships **license-free content only**: ICD-10-CM, HCPCS Level II, place
of service, CARC and RARC, and the *structure* for procedural coding. **CPT® is a
bring-your-own-license overlay**, imported at first run from the practice's own
licensed source.

The FR-8 move that makes this cheap is also the one that makes it smallest:
**the practice's own prior year of charge lines already is its CPT subset.** A
small ambulatory practice bills perhaps twenty to sixty distinct codes. Importing
them from the practice's own claim history yields the overlay it needs, with the
descriptions its staff already use, and it doubles as the seed for the offering
catalog — one import, two problems, and the smallest possible licensed surface.

**Its valve.** No overlay, no charge: first #3 cannot be attempted and says so by
name. A charge whose code is not in the overlay is a readiness blocker with a
one-click fix, never a silent pass-through to a claim that will reject in three
weeks. The overlay resolves by date of service and is versioned; the annual code
update is FR-5's year row, not an emergency.

**R-1 stands.** AMA licensing terms for practice-held CPT rights are unverified.
Until counsel returns, the repository ships license-free content only and the
overlay is loaded by the practice from its own source. If the answer comes back
badly, this step and the charge-capture design change — which is exactly why it
is isolated in one import rather than woven through the catalog.

## Progressive activation: the six firsts

**A first is a query, not a milestone object.** Each of the six is the earliest
Thread that reached a station carrying the required evidence. There is no
activation state machine, no checkbox, and no milestone record — which means a
first cannot be claimed, only earned, and the six rows recompute themselves. The
**Test/Production Firewall** applies: a synthetic patient can never satisfy a
first.

| # | The first, precisely | What turns on here | What stops in the legacy system | The evidence |
|---|---|---|---|---|
| **1** | A real patient booked into a real slot by practice staff, for a date on or after the read-only date | The schedule is authoritative here | Nobody books there | The appointment appears on tomorrow's huddle page |
| **2** | A real eligibility response received for a real patient on the schedule **and read by a person** — not a 271 in a log | Coverage on file here is the one staff trust | Nobody re-keys coverage there | A readiness line on the huddle page changed because of it |
| **3** | A charge created from a visit that happened after the read-only date, priced from the core catalog plus the practice's own overlay, and closed into a day | Charges are captured here | Nobody enters charges there | The charge appears in the day's close sheet |
| **4** | A claim **accepted by the clearinghouse** — a 999 or 277CA naming our claim — not merely transmitted | Claims go out from here for post-boundary dates of service | Nothing is submitted there for those dates | The acknowledgment, stored on the thread |
| **5** | A real remittance received, reassociated, posted to service lines, variance evaluated against the observed Promise, and reflected on a patient balance | Money answers land here | Nothing posts there for post-boundary claims | FD-8's live ERA, end to end |
| **6** | A day where the drawer and the card batch tie, a deposit exists on a bank line, every landed line is explained by exactly one thing, and the close sheet is signed | The day is the unit of completion here | The legacy daily close stops | The signed close sheet — the binder's first real page |

**They are a dependency graph, not a line — and the numbering hides it.** FD-9
lists the firsts in the order schedule → eligibility → charges → claims → remit →
banked day, which reads as a strict sequence. It is not one. Slice 2 delivers the
self-pay and time-of-service money path, the drawer, the card batch, and the
generated deposit slip **before** Slice 3 delivers claims and the 835. So the
practice's **first banked, balanced, and closed day will arrive before its first
remittance** — first #6 before first #5 — and that is correct rather than a
sequencing error, because a cash patient's day ties without a payer in it. The
real dependencies are: 1 → 2 → 3; 3 → 4 → 5; and 3 + any money path → 6. Recorded
as a discrepancy below.

**Each first is a tier-3 action.** Declaring a lane read-only in the legacy system
executes after a visible countdown unless cancelled, because it changes where
three other people do their work and they need to see it coming. It is not a
tier-4 hard stop: there is no second human to approve it, and every one of these
except #4 is reversible in an afternoon.

**The double-work budget.** The review's sharpest unhandled gap was that in a
three-person practice, go-live week means somebody does double work while still
answering the phone — and no document owned it. Progressive activation's entire
purpose is to make that double work happen **one lane at a time**, which is only
an improvement if the practice is told which lane and for how long. So each first
emits one line of a printed page:

> **What you are doing twice this week.** Appointments: only here, since Sep 2 —
> nothing to do twice. Charges: entered here and in MediTouch, until the first
> claim is accepted (waiting on the clearinghouse, submitted Aug 12). Payments:
> desk payments here; anything against an old balance still goes in MediTouch.
> **Two lanes doubled. Nothing else.**

That page is the honest answer to "when does this get easier," it names the
partner the practice is waiting on, and it shrinks visibly. It is also the
artifact that makes progressive activation feel like progress rather than like
limbo, which is the failure mode it is otherwise vulnerable to.

## Rollback honesty

Every incumbent implementation plan promises rollback and none of them can deliver
it after a claim exists. The product should say what is true.

**Rollback never deletes.** openPractice's history is append-only, so "rolling
back" a first does not erase the threads it created — it **re-points authority**
about where the practice does that work, and the threads stand, closed with a
dated disposition. This is the only definition of rollback consistent with FD-3,
and it is also the only one that survives an audit.

| First | Can it be rolled back? | What it costs | What cannot be taken back |
|---|---|---|---|
| **1 booked** | Fully | Print the schedule, re-enter it there. An afternoon. | Patients already told a new confirmation number or link — a communications fact, not a data fact |
| **2 eligibility** | Fully | Nothing. The payer portal was always the fallback and stays one. | Nothing |
| **3 charge** | While unbilled | Dated reversals here, re-entry there. A day of double work. | Nothing left the building |
| **4 claim accepted** | **No** | — | A claim exists at a payer under the practice's NPI, with a claim number, a payer control number, and a running timely-filing clock. The vocabulary changes here permanently: **void and replacement, not rollback.** |
| **5 remittance posted** | The posting is reversible; the money is not | Reversal entries; the ledger keeps both sides | The payer considers the claim adjudicated, the 835 was consumed, and if the funds landed there is a bank line that must still be explained |
| **6 banked, balanced, closed** | Only forward | Corrections land as dated reversals in the next day or the next batch | The deposit; an exported journal batch (frozen, FD-3); and the signed close sheet — evidence you can delete is not evidence |

**The ruling, in one line: there is exactly one point of no return in the
activation sequence and it is the first accepted claim — not go-live, which does
not exist.** Everything before it is a reversible operating choice about where
work happens. Everything after it is *correctable* but not *reversible*, and the
product must stop using the word "rollback" at that line, in its copy and in its
sales conversation, or it will be caught the first time a practice asks.

**The migration has a second, smaller line: the owner's signature.** Before it,
the entire opening-balance import is voidable as a set and re-runnable at tier 2.
After it, an opening balance changes only by dated reversal with an amended sheet
at tier 4, because the owner attested to a number and an attestation you can
quietly edit is not one. Two irreversible acts in the whole of day one. Everything
else can be put back.

**What rollback does not mean, and must never be sold as.** A configuration
mistake that produced forty wrong claims needs a claim-correction workflow, not a
config revert — the review flagged exactly this as the one rollback question that
matters and the one the prior atomic left undesigned. The answer is that
corrections are owned by the claims path (replacement, void, corrected claim with
the original payer control number), and the only thing day one owes them is the
provenance to find all forty: **every thread created or advanced by an import or
by a first carries the batch it came from**, so "everything that went out under
the wrong pay-to address" is one query rather than a hunt.

## There is no cutover weekend

The classic go-live weekend exists to do four things. Each is dissolved rather
than scheduled:

| What the weekend was for | What replaces it |
|---|---|
| **Freeze the source system** | The read-only date, thrown per lane, on an ordinary Tuesday, with a visible countdown |
| **Load the final delta** | There is no delta. Open items loaded once; everything since was born here |
| **Verify counts** | The signed reconciliation, which is an attestation against a report the owner already trusts, not a verification session |
| **Throw the switch** | There is no switch. There are six firsts, each earned once, each turning off exactly one thing |

The only genuinely scheduled event on day one is the first-run confirmation and
the code-set import — under an hour, doable on a Tuesday between patients, and
restartable if the phone rings in the middle. **Nobody works a weekend.** A
practice of two to four people has no spare capacity for one, and a weekend that
goes badly has no fallback but Monday.

The trade is stated plainly rather than hidden: progressive activation makes the
double-work window **longer and shallower** instead of short and total. Some
owners will say they would rather have one terrible weekend and be done. The
answer is that the terrible weekend has a failure mode with no exit, and that the
double-work budget page is what keeps the shallow version honest.

## Interfaces and boundaries

**Consumes.** The legacy aging report and whatever export the legacy system will
produce; the practice's W-9, one prior CMS-1500 or 837, one remittance, its
staff roster, and its prior year of charge lines; NPPES; the practice's own
licensed CPT source; the party and place register as substrate; the Roster for
owners; the Thread contract for minting, stations, conditions, and the
re-association key set; the four governance tiers; clearinghouse, merchant, and
per-payer enrollment status as declared Promises.

**Emits.** The migration boundary as a stated rule; Old-System Balance Threads;
the **signed opening reconciliation** and its closing counterpart; import rejects
as owned work; pre-openPractice promise-memory rows with their provenance stamp;
the run-off row and its monthly retirement arithmetic on the money page; the six
firsts as computed rows with their blockers named; the double-work budget page;
the setup one-pager; the exit-archive record; and a batch stamp on everything an
import or an activation created.

**This document owns day one and nothing else.** The seams, stated once:

- **[Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md)**
  owns the guided setup conversation, lazy discovery of configuration at the moment
  it first blocks something, the practice change log with undo, and the scheduled
  changes calendar. This document names only what must be confirmed before each
  first can be attempted. That atomic still carries the cutover control room,
  configuration promotion, and policy simulation that FD-9 retired, and is due its
  own revision; where the two disagree today, **FD-9 governs.**
- **[The Visit Thread](openpractice-small-practice-beta-visit-thread.md)** owns
  the migrated-balance type, the station vocabulary, the conditions, and
  re-association. This document requests three additive changes and invents no
  mechanics: that receiving money is legal on a migrated thread, that migrated
  threads carry the re-association key set as an optional facet, and that
  vendor-side enrollment gets a home in the non-visit enumeration.
- **Money Lines and the bank inbox** own the reason taxonomy, the GL destinations,
  gross-versus-net, and the to-zero worklist. This document says only that legacy
  money *arrives* and must have a destination; it does not define the destination.
- **The day rhythm** owns the huddle page and the close sheet. This document adds
  sections to both (the six firsts, the double-work budget) and authors neither.
- **Claims and the deadline control plane** own correction, replacement, and void.
  This document owns only the batch provenance that makes a bulk correction
  findable.

**It does not** create a cutover project, a control room, a configuration
promotion pipeline, environments, or a policy-simulation harness — FD-9 deleted
all five. It does not migrate AR detail, documents, plans, or stored cards. It
does not post a single dollar of imported evidence to the ledger. It does not
permit an imported coverage row or an imported balance to be treated as
independently verified. And it does not claim that a technically complete import
makes the practice's legacy obligations go away.

## Alternatives and tensions

**Full AR conversion versus open items only.** The case for full conversion is
genuinely attractive and is why practices keep buying it: one system, one
statement path, one login, no second license, no double entry, no patient who owes
money in two places. It is also the classic implementation killer — highest
effort, highest error rate, lowest durable value — and the balances it converts
are the least trustworthy data the practice owns. Open-items-only was taken, and
its costs are real and should be quoted rather than minimized: two statement
paths for months, a legacy license the practice keeps paying for, and patients
whose total balance no single screen shows.

**Summary threads versus detail.** Summary keeps the reconciliation cheap and
honest. The cost is felt weekly by whoever works the run-off: the answer to "why
does she owe $340?" is "open MediTouch." That friction is the whole reason the
practice will ask for the **adopt transition** in week three, which the beta does
not ship. The pressure is predictable and should be watched rather than denied.

**One-way boundary versus symmetry.** Letting a migrated thread receive money
while forbidding it to be worked is an asymmetry that will look arbitrary to
whoever implements it. The alternative — refusing legacy money entirely — leaves
permanently unexplainable lines on the bank statement and breaks D-4, which is a
differentiator. The asymmetry is the lesser cost, but it must be written into the
contract in words rather than discovered.

**Evidence-only import versus seeding nothing.** Seeding accelerates D-2 by
months. It also introduces a sample seam that will make the first drift digest
lie if the pooling is naive, and it creates a class of Promise whose provenance
staff must be taught to read. Seeding nothing is safer and slower; the beta
cannot afford slower on its second differentiator.

**Progressive activation versus a weekend.** Progressive is longer and shallower;
a weekend is short and total. A practice with an office manager who genuinely
prefers the weekend is not being irrational — she is choosing certainty about when
it ends. The counter is that she has nobody to cover the phone on Saturday and no
fallback on Sunday night.

**The reconciliation may simply not get signed.** The owner may decline because he
does not believe the legacy number either. The mitigation is the wording — he
attests to what the report printed, not to what is true — and the incentive is
that his whole-practice total stays suppressed until he does. If a practice still
will not sign, that is a finding about the artifact, and the beta should treat it
as one rather than as a compliance failure.

**"Rollback" is a word sales wants and the product cannot always honor.** Saying
plainly that the first accepted claim is a point of no return will lose a
conversation occasionally. Not saying it loses the customer once, permanently, at
the worst possible moment.

## Open questions

- **Discrepancy, recorded.** FD-9's six firsts are numbered as a sequence in which
  the first posted remittance precedes the first banked, balanced, closed day, but
  scope-and-sequencing places the self-pay money path (Slice 2) ahead of claims
  (Slice 3), so first #6 is reachable before first #5. This document treats the
  firsts as a dependency graph. The numbering should be restated, or the ordering
  claim dropped, in whichever document is revised next.
- **Discrepancy, recorded.** FD-8 tracks clearinghouse and merchant enrollment as
  "the implementation's first two threads," but the Thread contract's non-visit
  types are a closed enumeration of eight and its enrollment case is defined as
  provider × payer × location. Widening the counterparty or adding a ninth type
  are both additive minor bumps; the contract must choose, and the choice should
  also settle how a gating thread sorts when its derived dollars are zero.
- What grain does the design partner's legacy aging report actually print at, and
  does its export carry the re-association key set (payer, member id, date of
  service, provider, billed amount, payer claim control number)? Everything about
  inbound legacy remittances depends on the answer.
- Can that system export remittance history at all, and in what form (R-4)? The
  fallback ladder is defined; the answer changes when D-2 becomes demonstrable.
- **Does the patient money view show a whole-practice balance during run-off?** A
  patient who owes $40 here and $180 in MediTouch will pay $40 through the texted
  link and receive a legacy statement a week later. Showing the combined figure
  requires the pay link to accept money against a migrated thread; showing only
  ours is honest per-system and dishonest per-patient. The prohibition on
  *working* a migrated thread does not settle it, and the beta practice will raise
  it in month one.
- What is the script for payment-plan and autopay patients, how many are there
  typically, and does the practice accept losing plans as the cost of switching?
  This is the migration's most likely source of patient-visible harm.
- **Can a first regress?** A clearinghouse can de-enroll, a processor can freeze an
  account, an EHR connection can lapse. Is a first ever un-green, and if so does
  the legacy lane it turned off come back on, or does the practice work manually
  in openPractice through the manual connector?
- Who holds the legacy exit archive, in what form, for how long, and does
  openPractice store the bytes or only the manifest and hash? It is a retention
  question, a trust question, and possibly a storage-cost question.
- How long is the read-only period in practice, and does the retirement
  arithmetic proposed here actually move an owner to a decision, or does the old
  system live for three years because $180 a month is invisible?
- Does the run-off need its own closing reconciliation to be signed, and what
  happens to whatever balance is still open on the retirement date — write-off,
  collections, or a permanent tail of threads with no system behind them?
- Does the migration itself need an as-of view? An auditor asking what the
  practice believed on September 1 is asking about a boundary, not about a day.

## Relationships

Charter: [Framing Decisions](../review/openpractice-framing-decisions.md) **FD-9**
(progressive activation, open-items-only migration, the old-system balance thread),
with **FD-8** (kickoff enrollment threads and live-money acceptance), **FD-2** (the
cold start this document's evidence import exists to relieve), **FD-11** (the
first-run code-set import), **FD-3** (always-closed books, which is why rollback
never deletes), and **FD-5** (the tiers assigned to every act here).

Sequenced by
[Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md),
which places the migration plan and the evidence import in Slice 0, the signed
reconciliation and the defined read-only period in Slice 5, and carries risks R-1,
R-3, R-4, and R-5 that this document works against. Governed by the work model,
the eight-rule feature contract, and the capability register in
[Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md),
which names the four capabilities this document owns — **Progressive Activation
And The Six Firsts**, **Open-Items Migration And Balance Reconciliation**,
**Old-System Balance Thread**, and **Remittance History Import (Evidence Only)** —
plus **Code-Set Core And Licensed Overlay Import**, which it shares.

Binds to [The Visit Thread](openpractice-small-practice-beta-visit-thread.md) for
the migrated-balance type, station vocabulary, governance on transitions, and
re-association, and requests three additive changes to it. Complements
[Implementation And Configuration Lifecycle](openpractice-small-practice-beta-implementation-and-configuration-lifecycle.md),
which owns guided configuration and lazy discovery and is due its own FD-9
revision. Hands legacy money, the reason taxonomy, and the to-zero worklist to
[Money Lines And The Bank Inbox](openpractice-small-practice-beta-money-lines-and-bank-inbox.md);
hands the six-firsts strip and the double-work budget to
[The Day Rhythm](openpractice-small-practice-beta-day-rhythm.md) for rendering on
the huddle page and the close sheet.

Answers the implementation critique in
[Feat Review: Practice Administration](../review/openpractice-feat-review-practice-administration.md)
§5.1 (the already-running practice is the real beta scenario), §5.3 (migration
scoped brutally; run off old A/R where it lives), §5.5 (no cutover; "first of
each"), §5.6 (financial rollback needs compensating action, not reversal), and its
third top-level reframing, *"There is no implementation project; there is a first
one of each."* The run-off row, the whole-practice total it feeds, and the money
page it appears on are drawn from Page 3 of
[Paper Prototypes](../review/openpractice-paper-prototypes.md), which is this
document's visual acceptance target. Origin of the finding that forced the
sequencing rewrite is
[Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md) §6–§7.
