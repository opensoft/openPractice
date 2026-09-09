# The Day: Huddle, Close, And Practice Rhythm — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should make the Day the unit of completion — one printable page in the morning that lists only the people who need a person before they arrive, and one evening sheet that is the same computation run against the Day and ends in a signature — with exactly three shipped conditions that may stop a close, everything else rolling forward as a named owner and a date, deferral that names a date and a reason without ever touching the deadline or leaving its dollars out of the total, an unclosed day escalating only by appearing on every page until somebody signs it, whoever is here that evening able to sign it, and the week, month, quarter, and year reaching the practice as lines on a day's page rather than as four more screens.
Topics: openpractice-small-practice-beta, day-rhythm
Repository context: openPractice — the day, week, month, quarter, and year rituals for a small ambulatory practice beta, and the two printed pages that frame them
Captured: 2026-08-27

## Possible feats

- **The order the page prints in** — the morning sheet lists only the people who need a person before they arrive, in the order they walk in, with one sentence of what is missing and one of what fixes it; the ones who are fine get four lines, because their job is to be counted and not read.
- **Closing the day is subtraction, and the signature is the evidence** — the evening sheet is the morning page's own arithmetic run against the Day: everyone on the books accounted for, every dollar counted three ways, every page that arrived dispositioned — and one signed sheet that becomes the audit binder a day at a time.
- **Three reasons to stop, and a name and a date on everything else** — a shipped, closed list of what may not cross midnight, resting on one principle: the close stops only for facts that tomorrow cannot reconstruct.
- **"Not today, and here is why"** — deferral that names a date and a reason, leaves the age and the deadline untouched, keeps its dollars inside the owner's total, and comes back on the day it said it would.
- **A day nobody closed is the first line of tomorrow's page** — the only escalation is appearing, and it appears until somebody signs it; and anybody here can sign it, because the day belongs to the practice and not to the office manager.
- **Longer days: Friday afternoon, the third of the month, the quarter's folder** — the week, month, quarter, and year are the same open-and-close with different contents, shipped pre-populated, each reaching the practice as a line on a day's page rather than as four more screens.
- **One sheet, read aloud in four minutes** — designed to paper first: nothing that lives only in a hover, an expander, or a colour, every number carrying its denominator and its as-of time, and a page the practice still has in hand on the morning the system is down.

## Focus

What does a practice have to do to be finished with a Tuesday — and can that act be defined tightly enough that the checklist is rendered rather than authored, the evidence is exhaust rather than preparation, the day can always be signed without the signature becoming a rubber stamp, and the week, month, quarter, and year need no rituals of their own?

## Current state: the packet describes a flow; the practice lives in days

Five of the six cluster reviews described continuous work — queues that fill, metrics that accumulate, detectors that fire — and the operations review named the consequence outright: **"No day boundary anywhere. Queues are continuous; metrics are periodic; nothing owns 'the day is complete.'"** It named End-of-Day Close *the single largest omission in the cluster*, and four reviewers independently named a day ritual as their own cluster's largest omission, which is why FR-5 exists at all.

The gap is not aesthetic. Three specific failures follow from having no day boundary:

- **Money leaks at 5pm.** The drawer, the terminal batch, and the posted receipts are three counts of the same afternoon, and they only disagree at the moment somebody could still remember why. Tuesday's found $25 — a copay taken on the satellite's card reader at 9:45 and never posted — is discoverable at 5:34pm and unrecoverable in November.
- **Data quality leaks at midnight.** A charge posted Wednesday for Tuesday's visit silently restates Tuesday's numbers, and nothing in the packet would surface that.
- **"Did we do everything?" is unanswerable.** Without a boundary it is a feeling; with one it is a subtraction with a signature under it.

Two later results turned the omission into a shape. The paper prototypes ruled that **Close is not a seventh concept** — Page 2 is Page 1's computation run against a Day instead of against an appointment — which means the close checklist is never authored, only rendered, and a separate close-checklist model is deleted before it is built. And scope-and-sequencing made the artifact structural: **the End-of-Day Close sheet is the beta's integration test**, shipping in Slice 0 with only the sections the product can populate on day one, with every later slice adding exactly one section, and a slice that cannot add its section is not done.

That leaves this document owning a specific and unglamorous job. Five behaviours in the packet have no home except the day's two pages, and each of them is load-bearing somewhere else:

| Homeless behaviour | Where it lands |
|---|---|
| Governance tier 2's "surfaced in Today and in the close" (FR-6) | a named block on the evening sheet — *what the system did without being asked* |
| Governance tier 3's "visible countdown" (FR-6) | printed on both pages while it is still stoppable, and the sheet records whether anyone stopped it |
| Deferral that does not corrupt the aging numbers | the snooze contract below |
| The unowned unfinished item at 5:30pm | roll-forward: an owner and a date, printed on that date's page |
| Absence, at the one hour it hurts most | the Day's closer of record, derived from the Roster |

## Proposed feature set

### 1. The Day, and what a close is

**A Day is a date, a set of locations, an opening, and a closing.** It opens when the morning page is generated and closes when a Roster entry signs the evening sheet. It is the first of the six nouns and the only one that is a unit of *completion*: the Thread carries obligation, the Promise carries assertion, the Money Line carries cash, and the Day is what converts "in progress" into "done and provable."

**Close is Readiness applied to the Day.** What a finished day requires, minus what is true right now. The consequence is the design's largest single deletion: there is no close-checklist model, no checklist editor, no per-practice close template. The sheet is a rendering of a computation that already exists for appointments, pointed at a different subject.

Three properties of the Day are worth stating because a reader will otherwise assume the opposite:

- **An unclosed day is a missing signature, not an open accounting period.** Nothing is unposted, nothing is locked, nothing waits for the close to become true. FD-3 governs: the books are always closed, corrections are dated reversals, and the only lock in the product is export-batch freeze. If "unclosed day" is read as an open period, the whole of FD-3 is quietly undone.
- **The morning page is a rendering; the evening sheet is an artifact.** The morning page can be regenerated at any hour and will honestly differ from the one printed at 6:30 if facts changed. The evening sheet is **frozen at signature** — it is the thing a third party will judge the practice on, and a document that silently re-derives is not evidence. A later correction to a closed day appends a dated amendment that prints on *today's* sheet with a back-reference; the closed sheet is never edited. (This freeze rule is proposed here and is not covered by the Thread contract's freeze list, which governs Thread-owned artifacts; see Open questions.)
- **Tonight's close is tomorrow's first draft.** The evening sheet's last block is *what tomorrow needs*, computed at 5:41pm. The morning page re-derives at 6:30am and, where the two differ — an overnight cancellation by text, an ERA that landed at 2am — the morning page wins and says so in one line: *two changes since last night.* A page that quietly contradicts the page somebody read to the room yesterday is a page that loses the room.

**The acceptance test, answered.** *Which noun does it touch?* The Day, primarily; Readiness applied to it; the Roster for who closes; Threads for dispositions, roll-forward, and deferral; the Money Line for the counted-drawer slot. *Which surface?* Today, twice a day — the morning page and the evening sheet are one surface printed at two hours, which is exactly why they may never disagree. Plus a hook into The Money weekly and The Practice quarterly. *What does it delete?* See §11.

### 2. The morning page

Its design intent is settled by the prototype and is not reopened here: **only the people who need a person print, in the order they walk in**, each with one sentence of what is missing and one sentence of what fixes it — never a status name, never a percentage. The fourteen who are fine get four lines, because their value is being countable rather than readable.

The page's shipped blocks, in order, and why each is where it is:

| Block | Why here | Source |
|---|---|---|
| Yesterday's close, in one line | It is either reassurance or the day's most important fact, and it is one sentence either way | The previous Day |
| Who is here, with hours and locations | A page that says who is not ready without saying who is here is a trap | Roster ∩ Day |
| Derived coverage gaps — *Tomas is alone at the front desk 12 to 5* | Nobody typed it; it is the intersection of three people's hours | Roster ∩ Day |
| Open slots | Roster time on this Day with no Thread on it (C-1) | Roster ∩ Day minus Threads |
| Who needs a person, in walk-in order | The whole point of the page | Readiness over today's Threads |
| Who is fine, counted | So the count is trusted rather than skimmed | Readiness, negative form |
| Left over from yesterday | Roll-forwards, each with the name last night's close put on it | Threads with an owner and today's date |
| Clocks running out | Sorted by time remaining, not by elapsed time | Threads' clocks (A-2) |
| What sends today unless somebody stops it | Tier-3 countdowns, while they are still cancellable | FR-6 |

**Two sorts, not one.** The walk-in block sorts by appointment time, because it is read aloud in the order the door opens. The clocks block sorts by time remaining, because nothing anchors it to an hour. Readiness is not the *sort key* of either block; it is the **filter** that decides which rows print at all. The three binding inputs each say something slightly different about this and the difference is recorded in Open questions rather than resolved here.

**The generation rhythm.** Generated overnight and available at 6:30am — the review's finding is that a 6:30am email is by a wide margin the highest-adoption form of this page, readable on a phone in a parking lot — and printed when the first person arrives, stamped with both times. The printed copy is what gets read aloud; the screen copy is the same page with the fix beside each line turned into a button.

### 3. The close sheet

**It is a subtraction, not a report.** Everyone who was on the books must end the page accounted for. Every arrival must have charges or a person's name against a reason. The money is counted three ways, and the difference is printed *unresolved and then resolved*, so the sheet proves the check ran rather than asserting it.

The sheet grows one block per slice, which is what makes it the integration test:

| Block | Added by | What it asserts |
|---|---|---|
| Everyone who was on the books | Slice 0 | Booked = came in + cancelled + did not come + left without being seen, and walk-ins are added by name |
| Who was here, and who closed | Slice 0 | The Roster fact the signature attaches to |
| What the system did without being asked | Slice 0 | Every tier-2 action of the day, and every tier-3 countdown still running, with its stop control |
| What tomorrow needs | Slice 0 | Tomorrow's page, drafted at the only moment anyone will do it |
| What came in the door, and where it went | Slice 1 | Every page that arrived has a disposition or an owner and a date |
| Money we took in today | Slice 2 | Drawer, terminal batch, posted receipts, the deposit slip the product generated |
| Every visit has charges, or a name and a reason | Slice 3 | Including held claims, stated as *held, not sent, not denied* |
| Does it tie | Slice 4 | Bank lines explained by exactly one thing; what did not |
| What could make these numbers wrong | Slice 5 | The Data Trust line, and any detector that has been silent |
| The signature | Slice 0 | *Everything above is finished, or has a person's name and a date on it* |

Two of those blocks are doing work for other documents. **What the system did without being asked** is tier 2's only surface; if it is empty for a week, that emptiness is itself a signal and feeds the silent-failure canary. **What tomorrow needs** is where a tier-3 countdown is displayed to the one person who could still stop it — *the request sends at 6:00pm tonight unless somebody stops it* — and the sheet records the outcome, including "nobody stopped it," because a cancel window with no record of the non-cancellation is not evidence of anything.

**The sheet is the evidence because it had to be produced anyway.** Nothing in this product is ever *prepared* for an audit. One signed sheet per day in a folder is the audit binder, accumulated at the rate of one page a day, and the quarterly binder is that folder with a cover on it. The sheet's footer names the close-sheet version and the metric-pack version, so a reader in 2028 knows what the numbers on a 2026 sheet meant.

### 4. What may not cross midnight

This is the design's hardest call, and the tempting answer is wrong. A close that can be blocked by anything outside the practice's control — an unsigned note by a doctor who has gone home, a payer who never called back, a $25 difference nobody can find — is a close that gets skipped, and a skipped ritual is worse than a soft one, because it takes the evidence with it.

**The rule: the close stops only for facts that tomorrow cannot reconstruct.** Everything else is equally true in the morning and rolls forward with a name on it. Three shipped conditions qualify, and the list is closed — it is not authorable, per FR-1:

| Blocks the close | Why it cannot wait | How long it takes to clear |
|---|---|---|
| **A person on today's books with no disposition** | Tomorrow nobody will remember whether Mrs. Alvarez was here. The subtraction is the sheet's spine, and a spine with a hole in it is not evidence of anything | one tap per person, at the desk |
| **A drawer that was not counted** | Tomorrow's count cannot separate today's error from tomorrow's. The count is required; *tying* is not | under a minute |
| **A page that arrived with no owner** | A page with no name on it is exactly how a fax tray becomes a pile | one tap per page |

Note what each has in common: all three are inside the building, fixable by the person holding the pen, and destroyed by delay. And note what is deliberately *not* on the list — an unsigned clinical note, a payer who has not answered, an unexplained cash difference, an unworked denial, an unreconciled bank line. Every one of those is exactly as knowable tomorrow, and none of them is the practice's to finish tonight.

**The three blocking conditions cannot be deferred.** Snooze does not apply to them; that is the one place the deferral contract in §6 has no reach, and it is what keeps the block from becoming decorative.

**The one thing the close may never block on is a clinical fact.** An unsigned note is a Thread at `seen`, held, with the money clock it inherited from the date of service. The close names it, prints who is asking and when, and rolls it forward. A close held hostage by a physician who left at four is a close the practice will learn to skip.

### 5. Rolling forward with a name and a date

Roll-forward is the close's default disposition and the reason the close can always be signed without becoming a rubber stamp. It is not a deferral and not a task; it is **two writes on a Thread — an owner and a date — after which the item prints in that date's *left over* block.**

- **It is a transition, not a field assignment.** Per the Thread contract's consumer discipline, the Day declares itself the writer of exactly two transition types — *assign at close* and *defer with a reason* — and of no station whatsoever. The Day never moves a Thread along the line; it only names who must move it and when.
- **Its clearing condition is the underlying state, never a checkbox.** A roll-forward leaves the page when the Thread's station or condition changes. Nobody marks it done, which is what makes the *left over* block impossible to lie to.
- **The owner is always a Roster entry.** Never a payer, never a patient, never "the front desk," never unassigned. If nobody is scheduled to own it, the closer owns it, and if the closer will not own it, it is a decision and prints as one.
- **Repetition is printed, not escalated.** An item that has rolled forward five days running is not work; it is a decision nobody wants to make. On its third appearance it prints once with its count and its first reason — *this has been on the page since Thursday* — and if it carries dollars it appears on the owner's Friday page. There is no ladder above that, because at four staff the escalation target is the person who wrote the rule.

**The whole-day roll-forward.** When an outage takes a whole class of work — the EHR is down and no charges can be derived — the close prints one named item with one owner covering the class, not twenty-three separate ones. Twenty-three items is how a page becomes a wall, and a wall is abandoned exactly as fast as an inbox.

### 6. "Not today, and here is why"

Deferral is first-class because without it staff close valid work to clear red, and every aging number in the product becomes a lie within a month. The contract has six parts:

1. **It names a date and a reason.** The reason comes from a short shipped list plus free text — *the payer said call back on the 12th*; *the patient said they would mail it*; *waiting on somebody here who is out until Monday*; *going in Thursday's batch with the biller*; *not worth chasing yet*. The reason prints when the item returns: *you said Aetna would call back on the 12th. It is the 12th.*
2. **It sets a next-look date and touches nothing else.** The age keeps running, exactly as the Thread contract requires. **The deadline is not moved.** A deferral that could move a clock would be a filing limit anybody could postpone, and the clock is defined as derived and never typed. (The Thread contract's own work-model paragraph describes deferral as *setting the clock forward*; that sentence and the clock's derived-never-typed definition cannot both hold, and the discrepancy is recorded in Open questions rather than resolved here. This document is written against the derived reading, with a next-look date as a separate property.)
3. **It suppresses one thing only.** For its window, the item does not print in *needs a person* and does not count as stuck on dwell. It still counts as stuck when its **clock** expires, because that is a fact about a deadline and not about anybody's intention.
4. **It is capped by the clock.** A deferral that would run past the Thread's governing deadline is refused and re-offered at the deadline minus the shipped lead time. This is FR-6's deadline escape applied to a tier-1 action, and it is the single mechanism that stops deferral from becoming the hiding place.
5. **Its dollars stay in the total.** Deferred work is counted on the morning page — *six things are deferred; two come back tomorrow* — and its dollars remain inside the stuck total on the owner's money page. A deferral that removes money from the owner's total is a lie he will catch once and never forget.
6. **It is tier 1, and undoing it is silent.** Un-deferring returns the item immediately with its reason retained.

### 7. A day nobody closed

The escalation is **appearing**, and nothing else. At two to four staff, visibility *is* escalation; there is nobody to notify who is not already standing in the room.

| When | What happens |
|---|---|
| The next morning | The page's first line changes from *yesterday closed at 5:40pm, no days are open behind us* to *Monday never closed. Dana.* It stays there until somebody signs it |
| That week's money page | The owner's Friday page carries one Data Trust line naming which days it excludes. A weekly number computed over an unclosed day is annotated, never silently wrong |
| Two days running | The sheet offers a **combined close** covering both days rather than making somebody reconstruct two evenings. Capped at two — beyond that the days close individually, because a combined close that can cover a week is a weekly ritual wearing a daily costume |
| Any time later | The day remains closeable forever. Closing Tuesday on Thursday is legal and is stamped honestly: *closed Thursday 8:12am for Tuesday*, never a backdated 5:41pm. A close more than two days late is stamped *reconstructed* |
| That month's package | The bookkeeper's package names the unclosed days it contains, if any |

**What it must never do:** email the owner, page anybody, lock anything, or block tomorrow's work. A day that punishes the practice for a bad Tuesday is a day the practice stops closing.

**The empty day still closes.** A holiday, a snow day, or a day with no patients closes in about ten seconds: one line and a signature. Without this rule, January produces a wall of unclosed days and the first line of every morning page becomes noise.

### 8. Who closes the day

The prototype has Dana closing at 5:41pm. Nothing in the binding inputs says what happens on the Thursday Dana is out, and that Thursday is the whole point of the feature.

- **Everyone can close; one person is named.** Closing is not a permission and never becomes one. The Day carries a **closer of record** derived from the Roster — the person with the closing job who is scheduled that day, else whoever is scheduled latest at the primary location, else the owner. Never unassigned, and printed on the morning page so that at 7:50am the room already knows who is signing at 5:20pm.
- **When the named closer is out, the derivation just runs again.** There is no delegation object, no substitute-authority grant, and no handoff ceremony. Coverage is a fact about a day about a Roster member, and the closer falls out of it.
- **The sheet records who actually signed, not who was supposed to.** If Tomas closes because Dana went home at three, the sheet says Tomas, and Dana's morning page the next day says so too — which is most of what a while-you-were-out briefing is for.
- **Two locations, one Day, one signature — and two money blocks.** The satellite's drawer and card reader are physical objects in another building; the money block is per location and the day closes over both. This is not hypothetical: the prototype's found $25 was a copay taken on the satellite's reader and never posted, and a single merged money block would have hidden which building it came from. Whether the satellite's closer should sign anything is genuinely open.

### 9. The longer days

A week, a month, a quarter, and a year are longer days with their own open and close. The rule that keeps this from becoming four more screens is: **every longer rhythm reaches the practice as a line on a day's page.** There is no weekly screen, no monthly screen, no quarterly screen, and no annual screen. There are printed artifacts and there are days.

| Cadence | Opens | Closes | What prints | Owned by |
|---|---|---|---|---|
| **Day** | the morning page | the signed sheet | both pages | this document |
| **Week** | Monday's page | Friday's close, which gains one extra block — next week's expiring authorizations, unconfirmed appointments, who is out, and whether everybody booked can actually be billed | the owner's ten-minute money page, printed Friday afternoon; the payer drift digest as two or three payer behaviours | the money and claims capabilities; this document owns the slot and the input contract |
| **Month** | nothing opens | nothing closes | on the third: the bookkeeper's package with its one-page reconciliation certificate, the owner's one-pager, and the statement cycle running behind a visible cancel window | the accounting-export and reporting capabilities |
| **Quarter** | nothing opens | nothing closes | a cover on the folder of close sheets, plus the restore-drill certificate and the access review | compliance and platform capabilities |
| **Year** | nothing opens | nothing closes | risk analysis, contract renewal conversations, the code-set update, credential revalidation — each arriving as a dated line on the day it is due | the compliance to-do list |

**Month-end is printing, not a procedure (FD-3).** There is no month-end button, no period lock, and no close ceremony — a small practice will either be blocked by a ceremony or will skip it, and both are fatal. The month's artifacts are renderings of as-of queries over days that were already closed one at a time. The only lock in the product is export-batch freeze: once a journal batch is exported, that batch is versioned and immutable, and later corrections land in the next batch with back-references. The accountant gets stability; the practice never sees a lock. The package names how many amendments to already-closed days it contains, because that count is the honest version of "did anything move."

**The quarter and the year are referenced, not owned.** The binder is already written by the time anyone wants it. Recovery Day, the access review, the credential revalidation, and the annual risk analysis belong to their own capabilities; what this document contributes is the **Recurring Practice Calendar shipped pre-populated**, so the practice inherits an operating rhythm instead of being asked to invent one, and so each obligation appears on a day's page with an owner rather than in a calendar nobody opens.

### 10. Print is the constraint

Print-first is not nostalgia and not an export feature. It is a design constraint that materially improves the digital version, and it is testable:

- **One sheet.** US Letter, portrait, readable at arm's length. A page that needs two sheets has lost an argument about what matters. When a day is bad enough to overflow, the page prints *and six more, on the screen* rather than shrinking the type — a page that shrinks its type on the worst days is a page that fails on the worst days.
- **Read aloud in under four minutes** for the morning page; walked in under twenty for the close.
- **Nothing may live only in a hover, a tooltip, an expander, or a colour.** Colour may repeat what words already say and may never carry meaning alone. This one rule deletes an entire class of screen design that does not survive being printed, photographed, or read by somebody who does not distinguish red from green.
- **Every number carries its denominator and its as-of time.** *No-show 8% (4 of 51)* is trusted; *8%* is disputed. Every page states what it excludes.
- **Every line names a person or is a count.** There is no third kind of line.
- **No product vocabulary reaches either page.** These two sheets are the first place FR-2 is enforced, because they are literally printed and carried around: *money in, we don't know whose* rather than unapplied cash; *Medicare's wire was $212.40 less than the claims on it* rather than a payer-level adjustment; *16 days to refile this or we lose it* rather than an acceptance window.
- **The screen is the print plus one thing:** the fix beside each line is a button. That is the entire difference, and it is why the print is the specification rather than the output.
- **Reprints and amendments are stamped.** A page reprinted later says *reprinted*; a day amended after signature says so on the amending day's sheet with a back-reference.

**And the reason it matters most:** the morning of the outage, the printed page is the only page. The huddle was printed at 6:45, the practice has today's list in hand, and the close can be completed on paper and entered later, stamped *entered on ___*. A day ritual that only works while the product works is a ritual the practice will abandon on the one day it needed it.

### 11. What this deletes

Per the acceptance test's third question, and because six of the eight feature-contract rules delete surface rather than adding it:

- the close-checklist model and its editor — the checklist is rendered, never authored;
- the month-end close ceremony, the period lock, and the reopen-period workflow (FD-3);
- the escalation ladder, the acceptance window, and the service-level configuration screen — visibility is escalation, and every deadline comes from a fact already in the system;
- the delegation subsystem, the substitute-authority grant, and the handoff acceptance handshake — an owner and a date replace all three;
- the separate weekly, monthly, quarterly, and annual screens — four navigation entries become lines on days;
- the standalone "recurring obligations" list — a recurring obligation is a Thread with a date, and it prints where everything else prints;
- the notification email for work — the page is the notification;
- the "did we do everything today" question, which stops being a feeling and becomes a subtraction with a signature under it.

### 12. Governance, verdicts, artifacts, and rhythm

**Every verdict this document computes, and the valve it feeds (FR-3).** Nothing here is a score somebody reads.

| Verdict | Its valve — what changes when it changes |
|---|---|
| A booked person has no disposition at close | the signature block does not print; the sheet prints the gap with one-tap dispositions beside it |
| The drawer was not counted | same |
| A page that arrived has no owner | same |
| Anything else unfinished at close | roll-forward: an owner and a date are written onto the Thread, and it prints in that date's *left over* block |
| An item has rolled forward three or more times | it prints once with its count and first reason, and joins the owner's Friday page if it carries dollars |
| Deferred until a date | suppressed from *needs a person* and from dwell-derived stuck for the window; **not** removed from the count, and **not** removed from the stuck-dollar total |
| A deferral would run past the Thread's clock | refused, and re-offered at the deadline minus the shipped lead time |
| Yesterday has no signature | it is the first line of every morning page until signed; it annotates the week's money page; it is named in the month's package |
| The Day's closer is out | the Roster derivation reruns and prints the next name; never unassigned |
| A tier-3 countdown is still running at close | it prints with its deadline and its stop control, and the sheet records whether anybody stopped it |
| A tier-2 action occurred today | it prints in *what the system did without being asked*; a week of empty blocks feeds the silent-failure canary |
| The close took longer than the shipped time budget | **labelled a report.** Consumed by the product team, never shown to the practice, because it is a verdict about the software and not about the day |

**Governance tier of every action this document introduces (FR-6).** Nothing in the Day is tier 4 — the Day moves no money; the money actions performed inside the close carry the tiers assigned by their owning capabilities.

| Action | Tier | Undo means |
|---|---|---|
| Generate or print either page | none | no effect to undo |
| Defer with a reason | 1 | un-defer, silent and immediate; the reason is retained |
| Roll forward (assign owner and date) | 1 | reassign; the previous assignment stays in history |
| Stop a running tier-3 countdown from the sheet | 1 | cancelling is always tier 1 |
| Sign the close | 1 | **reopen**, which is a dated append printing on today's sheet — never an edit to the signed one |
| Amend a closed day (a late charge, a found difference) | 2 | prints on today's sheet with a back-reference to the closed day |
| Sign with an unresolved money difference | 2 | the difference prints named on the sheet and on the owner's Friday page |
| Reconstruct a day more than two days late | 2 | stamped *reconstructed on ___ by ___*, and it does not claim yesterday's hour |

**Rhythm and clearing conditions (FR-5).** Every work-producing feat states how its work clears; a work type whose only clearing condition is "a human marks it done" has to justify itself, and none of these need to.

| Feat | Rhythm slot | Clears when |
|---|---|---|
| The morning page | daily, generated overnight, printed on arrival | the day closes |
| The close sheet | daily, started around 5:20pm | it is signed |
| A roll-forward item | the date it was given | the Thread's station or condition changes |
| A deferral | returns on its date | the station changes, or the date arrives |
| An unclosed day | every morning page | somebody signs it |
| The week's look-ahead block | Friday's close | Monday's page |
| The month's printing | the third | the package is generated and its certificate signed |
| A quarterly or annual obligation | the day it is due, as one line | the owning capability's own completion, which is what emits its evidence |

**Artifacts, and the minutes they save (FR-4).**

| Artifact | Who touches it, when | What it replaces |
|---|---|---|
| The morning page | everybody, four minutes at 7:50 | the manager reading four screens and remembering the fifth |
| The signed close sheet | the closer at 5:41pm; an auditor once a year | assembling an evidence binder from nothing, twice a year, badly |
| Tomorrow's first draft, inside the close | the closer | rebuilding the morning list from scratch at 6:30am |
| The folder of close sheets | the quarter's binder | a binder that is *prepared* rather than accumulated |
| The deposit slip and the money block | the closer, at the drawer | a reconciliation done in November for an afternoon in August |

### 13. Exceptions, outages, and the bad days

| Situation | What the Day does |
|---|---|
| No patients (holiday, weather, provider out) | one line, one signature, about ten seconds |
| The practice closed at noon | the day closes at the hour it actually closed, stamped with that hour |
| The EHR is down and no charges can be derived | one whole-day roll-forward with one owner and one sentence, not twenty-three items |
| The clearinghouse is silent | nothing blocks; the silent-failure canary owns the detection, and the Data Trust line on the sheet names it |
| openPractice itself is down at 5pm | the close is completable on a printed form and entered later, stamped *entered on ___*; the morning page was already printed at 6:45 |
| No printer | both pages are readable on a phone; the print constraint means they still fit |
| A late charge arrives for a closed day | it is dated to the visit for revenue, prints as an amendment on today's sheet with a back-reference, and the closed sheet does not change |
| The closer forgot and went home | see §7 — it appears, and it keeps appearing |

### 14. Metrics, and who consumes them

| Number | Consumer, and what it changes |
|---|---|
| Days closed with a signature, of the last ten business days | beta acceptance — scope-and-sequencing requires ten of ten |
| Roll-forward count per close | the closer, on the sheet; a rising count is the honest early warning that the practice is falling behind |
| Items that have rolled forward three or more times | printed as decisions; on the owner's Friday page when they carry dollars |
| Deferrals outstanding, and how many return tomorrow | the morning page's count line, so deferral is never invisible |
| Deferrals that returned unchanged twice | the shipped dwell defaults are wrong for that work type, which is a product input rather than a practice one |
| Minutes from close start to signature | the product team only, and labelled a report |

## Interfaces and boundaries

**Consumes.** Threads, for every disposition, clock, dollar figure, and owner the two pages print — declared as *reads visit-thread ≥ 1.0*, per the contract's consumer discipline. The Roster, for who is here, their hours and locations, coverage and absence, and the closer derivation. Readiness, as the computation that both pages render; the Day applies it to a new subject and does not own it. Money Lines, for the drawer, the batch, the deposit slip, and the three states of expected, landed, and explained. Promises, for every date a clock is derived from. The Pile, for what arrived. The metric pack, for the numbers and their frozen formulas.

**Emits.** The Day record, with its opening, its closing, its closer, and its signature. The morning page, as a rendering. The close sheet, frozen at signature and accumulating into the binder. Two Thread transition types and no others — *assign at close* (owner and date) and *defer with a reason* (next-look date and reason) — with the Day declared as the writer of both. Tomorrow's draft. The Data Trust line naming which days a longer-period number excludes.

**Explicitly does not own.** The readiness computation, which is one evaluator used by every surface. The money arithmetic — the Daily Balanced Day is a money capability, and the Day owns only its *slot* in the sheet and the rule that a drawer must be *counted* even though it need not *tie*. The metric formulas. The compliance obligation catalogue. The binder's non-close contents. The coverage-day capability itself, which belongs to the Roster. Every patient-facing artifact. And no station on any Thread, ever.

**The boundary with The Money is a time horizon, not a subject.** Today holds what is actionable today; The Money holds the same Threads rolled up when they are not. Saying so is what stops the two surfaces being built twice, and it is why the weekly money page is a *hook* here and a *product* there.

**The boundary with the EHR runs one way.** The close reads whether a clinical fact has arrived and who signed it, never the fact itself, and it never blocks on one.

**Smallest useful slice.** Slice 0 ships the Day itself: both pages, the blocking rule with only its disposition condition active, roll-forward, deferral, the unclosed-day line, the closer derivation, and the signature. A day can be opened and closed with a signature before there is a single dollar in the system, and that sheet is already the first page of the binder. Every later slice adds exactly one block, and a slice that cannot add its block is not done.

## Alternatives and tensions

**A close that can always be signed can become a rubber stamp.** This is the real cost of §4's rule, and it is not fully mitigated. Three things push against it: the sheet is a subtraction and the arithmetic must balance, so a hole is visible rather than merely unaddressed; the money difference prints unresolved and then resolved, so the sheet proves the check ran; and the roll-forward count travels to the owner's Friday page, so a practice that signs everything and finishes nothing shows up as a rising number on somebody else's desk. If the beta shows staff signing a sheet with fourteen roll-forwards on it every night, the rule is wrong and the honest fix is a fourth blocking condition, not a lecture.

**A stricter close is more defensible and less likely to be performed.** The stricter alternative — nothing crosses midnight, ever — produces a beautiful audit trail at a practice that stopped closing days in week three. The chosen rule is deliberately weaker in exchange for being performed nightly, which is the only property that makes the binder real.

**Freezing the sheet means the binder contains pages that disagree with today's truth.** That is correct behaviour and will read as a bug at least once. The reconciliation is the as-of query and the amendment back-reference; the risk is that a reader compares a signed sheet to a live screen and concludes the product is wrong. A visible *as signed on ___; two later amendments* stamp is the mitigation, and it is not free.

**One sheet means somebody gets cut on the worst day.** The eleventh person who needs a person is the one most likely to be the expensive one. Printing *and six more, on the screen* is honest but does mean the room hears ten names and not sixteen. The alternative — let the page grow — produces a page nobody reads aloud, which loses all sixteen.

**Deferral is the designed hiding place, and it is only as good as its cap.** Capping by the clock, keeping the dollars in the total, and counting deferrals on the page are three fences around one hole. A staff member who wants to hide work can still defer everything to next Friday, and the only thing that catches it is that the money never left the owner's total.

**Two rituals a day is a real tax.** Four minutes plus twenty is roughly two percent of a four-person practice's administrative hours. The defence is that both replace work already being done — badly, in somebody's head, at the wrong hour — and that the close's twenty minutes eliminate the November afternoon spent reconstructing an August day. If the beta finds the close taking forty minutes, the product is at fault and the metric in §14 is how that gets known.

**Visibility as the only escalation depends on somebody reading the page.** At two to four staff that assumption holds, because the page is read aloud. It stops holding somewhere north of fifteen, and the packet's frame rule says that is fine — but the frame governs surfaces, not structures, and the unclosed-day mechanism should not acquire a notification engine when it stops holding.

**One Day across two locations is a choice the drawer argues with.** Money is physical and per-building; the day is one thing the practice experiences. The chosen shape — one Day, one signature, per-location money blocks — may not survive a practice with three sites and different closing hours.

**The combined close is humane and slightly corrosive.** Capping it at two days is an arbitrary number chosen to keep it from becoming a weekly ritual. It is the kind of number that should be measured rather than defended.

## Open questions

- **The deferral–clock discrepancy, and it is the sharpest one.** The Thread contract's work-model section says deferral *"sets the clock forward and leaves the age untouched"*; the same document's property table and feature-normalization both define the clock as *derived, never typed*, showing the nearest deadline that costs money. Both cannot hold: a typed clock is a filing limit anybody can postpone. This document is written against the derived reading, with a **next-look date** as a separate property that suppresses dwell-derived stuck and nothing else. Since the Thread contract governs Thread mechanics, the resolution is that document's to make — but it should be made before deferral is built, because the two readings produce different databases.
- **Does a frozen close sheet belong to the Thread contract's freeze list, or does the Day need its own?** The contract freezes what a third party will judge the practice on — the claim as submitted, the estimate as delivered, the remittance as received, the GFE as issued — all Thread-owned. A close sheet is judged by an auditor and is owned by the Day, so it falls outside that list while needing exactly the same rule. Either the freeze rule generalises beyond Threads or the Day gets its own, and the packet currently has neither.
- **What exactly is the morning page's sort?** Three binding inputs say three adjacent things: feature-normalization says *the huddle page sorts by clock*; scope-and-sequencing's D-1 says *readiness is the sort order of the day*; the prototype prints the walk-in block in appointment order with a separate clocks block sorted by time remaining. This document reads readiness as the **filter** and uses two sorts, which reproduces the prototype exactly — but the three statements should be reconciled in one place rather than in each consumer.
- **Do the daily five numbers print on either page?** The Today surface is defined as carrying *today's five numbers* and *what is stuck by dollars × clock*; the printed prototype huddle carries neither. Either the sheet is a subset of the surface, or the surface description is describing the screen. This document assumes the first and puts yesterday's numbers on the close sheet, where they are final, rather than on the morning page, where they are not.
- **Is there a roll-forward count at which the close should refuse to be signed** — and would refusing be honest or theatrical?
- **One Day or one Day per location**, and does the satellite's closer sign anything?
- **Who is the closer of record when the Roster shows nobody scheduled after 3pm** — a satellite half-day, a locum, a Saturday clinic staffed by one person who is also the provider?
- **Which day's sheet owns a cancel window that crosses midnight or a weekend** — a statement batch queued at 5pm Friday to send Monday at 9am is stoppable for sixty-four hours across three days' pages, and it is not obvious which page should show the control or who is responsible over a weekend.
- **How long is the binder, and what happens when a signed sheet with patient names on it meets a deletion obligation?** The close sheet is patient-identifying paper, and the packet's unresolved append-only-versus-required-deletion risk reaches it. A frozen artifact and a retention limit are the same collision as the ledger's, arriving on paper.
- **Does anything the close produces have a daily consumer outside the practice**, or is the daily artifact purely internal until the month prints?
- **Does the tier-3 countdown block survive the constitution question?** The close sheet is where *sends at 6:00pm unless somebody stops it* is displayed. If the open constitutional risk resolves toward requiring an affirmative click, that block changes shape and the close acquires an approval queue, which is the one outcome the governance tiers exist to prevent.

## Relationships

Bound by [The Visit Thread](openpractice-small-practice-beta-visit-thread.md), which is authoritative for every station, condition, clock, disposition, and transition this document renders; the Day writes exactly two transition types and no station. Governed by the ratified [Framing Decisions](../review/openpractice-framing-decisions.md) — FD-3 above all, since always-closed books are what make month-end printing and an unclosed day a missing signature rather than an open period, and then FD-5 for the tiers the close surfaces, FD-6 for the sheet as exhaust rather than preparation, and FD-1 for why there is no escalation ladder and no delegation object. Implements FR-5 in full and is the packet's home for it; exercises FR-1, FR-2, FR-3, FR-4, and FR-6 from [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md). Placed by [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) in **Slice 0, extended by every later slice**, where the close sheet is named the beta's integration test.

Its two surfaces are Pages 1 and 2 of [Paper Prototypes](../review/openpractice-paper-prototypes.md), whose composition verdict established that Close is Readiness applied to the Day and that the close checklist is rendered rather than authored; that document is the visual acceptance target for both pages. The proposals for End-of-Day Close, the Morning Huddle Page, snooze with a reason, the Recurring Practice Calendar, the Practice Health One-Pager, and print-first design come from [Operations And Intelligence](../review/openpractice-feat-review-operations-and-intelligence.md), whose cluster-level finding — *no day boundary anywhere* — this document exists to close.

Sibling Tier-1 atomics written in this round, each of which adds a block to the close sheet or a line to the morning page:

- [Phone, Fax, And The Inbound Pile](openpractice-small-practice-beta-phone-fax-and-inbound-pile.md) — owns *what came in the door and where it went*, and the rule that a page with no owner may not cross midnight.
- [Money Lines And The Bank Inbox](openpractice-small-practice-beta-money-lines-and-bank-inbox.md) — owns the money block, the deposit slip, the daily balanced day inside the close, and the owner's weekly money page this document supplies the slot for.
- [Self-Pay, Time-Of-Service, And The Good Faith Estimate](openpractice-small-practice-beta-self-pay-and-gfe.md) — owns the desk money the close counts and the *estimate due today* line on the morning page.
- [Enrollment-Gated Billing](openpractice-small-practice-beta-enrollment-gated-billing.md) — owns the held-claims line on both pages and the retro-release the morning page's dollar figure is waiting for.
- [Secondary And Corrected Claims, And The Deadline Control Plane](openpractice-small-practice-beta-secondary-corrected-claims-and-deadlines.md) — owns the clocks the morning page's last block prints and the deadline that caps every deferral.
- [The Roster, Access, And Offboarding](openpractice-small-practice-beta-roster-access-and-offboarding.md) — owns who is here, the derived coverage gaps, and the closer-of-record derivation this document consumes.
- [Day One: Migration And Progressive Activation](openpractice-small-practice-beta-day-one-migration-and-activation.md) — owns the sixth of the six firsts, *the first day banked, balanced, and closed with a signature*, which is this document's own acceptance event.

No existing document is modified by this one.
