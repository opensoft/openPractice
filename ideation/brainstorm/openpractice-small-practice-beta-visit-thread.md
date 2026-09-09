# The Visit Thread — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should mint one durable thread per visit-intent — at intent rather than at arrival — carrying a station, an age, a clock, a dollar amount, and an owner, with a fixed ten-station vocabulary, three blocking conditions, dated and attributed transitions reversible at the four governance tiers, eight enumerated non-visit thread types, and a versioned contract whose only justification for storing anything is a named rendering that consumes it.
Topics: openpractice-small-practice-beta, visit-thread, correlation-identity
Repository context: openPractice — whole-product analysis and feature framing for the first small group practice or small clinic beta
Captured: 2026-08-27

## Possible feats

- **One thread per intent** — mint a durable identity the moment a visit is intended (a booking, a walk-in presenting at the desk, a waitlist entry, an inbound referral or fax that implies a visit), so readiness, dispositions, clocks, and money have something to attach to before anyone arrives.
- **Stations with legal moves** — a fixed ten-station vocabulary and an enumerated transition table, where every move is dated, attributed to a person or a named rule, justified by a fact, reversible at a declared governance tier, and rejected outright if the move is not legal.
- **The clock beside the age** — carry time remaining to the nearest deadline that costs money, alongside elapsed time, so the day sorts by what dies next while the money page sorts by what is rotting.
- **Named non-visit threads** — eight enumerated types that never had a visit, each declaring what its station, clock, dollars, and owner mean, so nothing that owes an outcome is structurally invisible.
- **Renderings, not a trace** — the appeal packet, the secondary-claim autofill, the retro-rebill list, the stuck-money worklist, the patient's bill story, undo, and the day's close sheet as the only admissible reasons to store anything on a thread.
- **Reschedule as a move, not a rebirth** — one station change with targeted invalidation, so the estimate, the eligibility answer, the previsit list, the issued link, and the queued reminders survive everything that did not actually change.
- **A versioned contract with declared readers** — every consumer names the version it reads, every thread is stamped with the version it was minted under, and history is never rewritten to suit a newer version.

## Focus

What single correlation identity can carry a visit's whole administrative and financial life — and the lives of the things that never were visits — precisely enough that provenance, undo, worklists, and the patient's own "what happened to my bill" are all renderings of it rather than four systems built beside it?

## Current state: four spines describing one object

The feat review found four cross-cutting contracts, each claiming to be *the* spine, none aware of the others: revenue cycle's per-visit financial thread, platform's end-to-end transaction trace, platform's correlation-as-first-class-data, and the AI per-interaction decision record. The platform reviewer flagged two of the collisions itself and warned that "one of the two must be authoritative or both will be half-built." Practice administration's readiness graph made a fifth claim from a different direction, and front office's readiness contract and previsit plan computed the same diff twice.

Four spines is not four features. It is provenance built twice and joined never — an appeal packet assembled from one store, a secondary claim autofilled from another, an undo that can only reverse what its own trail happened to record, and a patient-facing bill story that cannot see either. FD-7 ratified the resolution: **one thread identity, written as a versioned contract before any consumer**, with the other three descriptions collapsing into views over it.

The paper prototypes then made the shape non-negotiable in three specific ways. Page 1's entire sort order attaches readiness to appointments that have not happened, and Page 2 disposes two cancellations and two no-shows — neither block has an object if the thread is minted at arrival (amendment A-3). Every page displays time remaining, which a thread carrying only an age cannot produce (amendment A-2). And every one of the six pages that arrived in Tuesday's fax tray became a thread on arrival and left the Pile the moment it had a disposition, which held perfectly and is the hardest behavior in the packet to get right (clarification C-2).

This document is written first because Money Lines, Secondary Claims, Enrollment-Gated Billing, The Day, and Phone/Fax/Pile all bind to it. Nothing below is a schema. It is the contract those documents may assume.

## What a thread is

**One thread per visit-intent.** A thread is minted the moment someone means for a visit to happen and is never minted twice for the same intent. Four events mint one:

| Minting event | Example from the fixture | Station at mint |
|---|---|---|
| A booking | Marguerite Bell books for Tuesday 8:00 | `booked` |
| A walk-in presenting at the desk | Rosalind Pike, added Tuesday 11:30 | `arrived` |
| A waitlist entry | someone asks for the first opening with Rosa | `intent` |
| An inbound page that implies a visit | Della Frank's referral, faxed Aug 20 | `intent` |

The fourth line is the one that pays for the whole design. Della Frank's fax became a thread when it landed in the Pile on Aug 20, sat at `intent` unmatched for five days, and did not become a *second* object when she was booked — it moved to `booked`. The Pile item and the visit are the same thread at two stations. That is why Page 2's Pile section could dispose six pages against six owners with six clocks and never invent a container.

### The five properties, at any instant

| Property | What it is | Rules |
|---|---|---|
| **Station** | Where the thread is on the line, plus the condition it is in there | A pair: one of ten positions, and one of `moving` / `held` / `stuck` / `disputed`. Never null. |
| **Age** | Time since the thread was minted, and time at the current station | Two numbers. Age answers *is this rotting?* |
| **Clock** | The nearest deadline that costs money if missed, its name, and the time remaining | May be empty. The clock answers *when does this die?* |
| **Dollars** | The money at risk right now: an amount, a direction, and a counterparty | Single-valued. A thread appears in exactly one row of any money rollup. |
| **Owner** | The roster entry who must act next | Always a Roster entry — Dana, Tomas, Marcy at Ledgerline. Never a payer, never a patient, never "the system." A robot is a roster entry (FD-5, the roster model). |

Alongside those five, a thread carries an identity: its id, its type, the contract version it was minted under, and **references** to the parties, places, promises, money lines, and clinical facts it depends on. It carries references, not copies, with one deliberate exception stated under "What the thread freezes."

**The dollar amount means something different at every station, and the contract names which quantity it is.** This is what keeps Page 3's stuck table from double-counting: at `booked` it is the estimate; at `charged` it is billed charges; at `claimed` it is the expected allowed amount from promise memory (FD-2), not the charge; at `remitted` it is whatever balance remains and whose it is; at `banked` it is zero and the thread is on its way to `closed`. A thread is in one place with one number owed by one party, which is why the six rows of "Where is our money sitting?" sum to a figure the owner will believe.

**The clock shows one deadline: the nearest one that costs money.** A thread frequently has several — the payer's filing window derived from the date of service, an authorization's expiry, a GFE's dispute window, a demand letter's thirty days, a statement cycle. The contract picks the nearest and names it in plain language, because a page showing four countdowns per row is a page nobody reads. The runner-up deadline is retrievable and is not displayed. Where no deadline exists, the clock is empty and the thread can still go `stuck` on dwell.

### The station vocabulary

Ten positions. **Six are universal and four are visit-only.** The universal six are defined by what they mean about obligation, not by what they mean about billing, which is what lets a records request and a recoupment case live on the same line as a visit:

| Station | Universal meaning | On a visit |
|---|---|---|
| `intent` | An outcome is owed. Nothing has been committed. | Waitlist entry, unmatched referral, a call that created an obligation |
| `booked` | *visit-only* | A time, a provider, and a place exist |
| `ready` | *visit-only* | Nothing is left that must be true before arrival |
| `arrived` | *visit-only* | The patient is here, or the telehealth call connected |
| `seen` | *visit-only* | The visit happened |
| `charged` | Our side is complete and priced. | Billable facts exist here, coded |
| `claimed` | Something left the building; we are waiting on someone outside. | The claim was transmitted |
| `remitted` | The outside party answered. | An ERA, an EOB, a denial, a determination |
| `banked` | Money moved and a bank line proves it. | The deposit, the wire, the settled batch |
| `closed` | Nobody owes anybody anything on this thread. | Done, and provable |

Station names are internal. **Each station carries a per-type plain-language label**, so the same position reads "Waiting on Aetna" on a claim, "Sent to Aetna, waiting" on a records request, and "We asked, they haven't answered" on the patient's own page.

`ready` deserves a note, because readiness is a computation and not a state anyone stores. The station `ready` is a **latch on that computation**: a thread enters it automatically when readiness returns empty and leaves it automatically when readiness stops being empty. Anthony Pruitt was `ready` until BCBS said his plan ended Aug 1, at which point he dropped back to `booked` and appeared on Tuesday's page. The thread stores the *transition and its reason*, never the verdict — which is precisely the missing valve the review named twelve times: the station change is what consumes the verdict.

### The three conditions

The exception states are not positions on the line. They are the reason a thread is not moving, attached to the station it is sitting at. Rosalind Pike's claim is at `charged`, held — "charged, claim HELD," exactly as the close sheet printed it. Making these positions would erase the position they qualify and make retro-release uncomputable.

| Condition | Who sets it | What it requires | How it ends |
|---|---|---|---|
| **held** | Declared, by a person or a shipped rule | A structured release condition and an owner | Automatically, when the condition resolves — and it releases *every* thread the condition held, in one action |
| **stuck** | **Never set. Always derived.** | Nothing | The thread moves |
| **disputed** | Declared, with an external counterparty | Its own clock and a suppression list | The counterparty answers |

**`stuck` is derived and cannot be typed.** A thread is stuck when its clock has expired, or when it has no clock and has sat at its station longer than that station's shipped dwell. Nobody marks work as stuck; if they could, the aging signal would be a lie inside a month. This is the whole definition of work, and it is developed under "The work model" below.

The shipped hold reasons are a closed list — no hold authoring, per FD-1's frame rule: enrollment not yet effective for this provider × payer × location; waiting on a clinical fact; waiting on an authorization; waiting on a payer's answer to a records request; an open appeal or variance on the same money; and the tier-3 delay window while it is running. Each names its release condition structurally, which is what makes retro-release a query rather than a hunt.

**`disputed` suppresses.** While a GFE dispute or an appeal is open on money, no statement for that money leaves the building, and no collections cadence advances. That is the "open variance / open appeal → hold on the patient statement" valve the review found had no consumer.

### Legal transitions

Moves not listed are rejected, not warned about. Stations may repeat; a thread is not one pass down a line.

| From | To | Occasion |
|---|---|---|
| `intent` | `booked` | A slot is assigned — the referral becomes an appointment |
| `intent` | `closed` | Never converted: waitlist expired, referral declined, page was not ours |
| `booked` | `ready` | Readiness returned empty |
| `ready` | `booked` | Readiness stopped being empty — a coverage change, an expired authorization |
| `booked` | `booked` | **Reschedule** — see below |
| `ready` | `booked` | Reschedule from a ready thread; readiness re-evaluates against the new facts |
| `booked` / `ready` | `arrived` | The patient is here. Readiness never blocks this move (§6.9: hold the claim, never the patient) |
| `booked` / `ready` | `closed` | Cancelled, or a no-show with no fee, with the disposition recorded |
| `booked` | `charged` | A no-show fee is billed |
| `arrived` | `seen` | The visit happened |
| `arrived` | `closed` | Left without being seen; disposition recorded |
| `seen` | `charged` | Billable facts arrived from the EHR |
| `charged` | `claimed` | The claim was transmitted |
| `charged` | `banked` | Self-pay, paid at the desk — no payer, no claim |
| `charged` | `closed` | Zero-dollar, or written off at tier 4 |
| `claimed` | `remitted` | An answer arrived: payment, denial, or determination |
| `claimed` | `charged` | Rejected before adjudication — it never reached the payer, so it was never a claim |
| `claimed` | `claimed` | Corrected claim, replacement, or resubmission |
| `remitted` | `claimed` | The next payer, an appeal, or a corrected claim |
| `remitted` | `banked` | The money landed on a bank line |
| `remitted` | `closed` | The balance resolved to zero without money moving |
| `banked` | `remitted` | A later answer on the same visit — the secondary, the patient's turn |
| `banked` | `closed` | Nothing left |
| `closed` | *any prior station* | **Reopen** — a late ERA, a chargeback, a recoupment. Dated, attributed, and never an edit |

Two rules govern every row. **A transition that cannot name the dated fact that caused it is not a transition** — an ERA line, a signed note, a terminal batch, a bank line, a person's click. And **the thread never rewinds.** Reopening `closed` is a forward move that appends; correcting a sent claim is a replacement, not an erasure. This is FD-3's always-closed books expressed on a single object: corrections are dated reversals, never edits, so the appeal packet and the as-of query stay true by construction.

### Reschedule

Reschedule is `booked → booked` on the same thread — never cancel-plus-create, which orphans four artifacts silently. What survives is the default; **invalidation is targeted to what actually changed**:

| What changed | Invalidated | Survives |
|---|---|---|
| Time only, same day | Queued reminders (re-queued to the new time) | Estimate, eligibility answer, previsit list, issued link, authorization, readiness |
| Date, same provider and place | Reminders; eligibility if the new date crosses a coverage boundary; the estimate if a price change is effective between the dates | Previsit list, issued link, authorization if still within its units and expiry |
| Provider | Enrollment readiness (re-evaluated against the new provider's promises); the estimate if the price differs | Eligibility, previsit list, issued link |
| Location | Place-of-service on the eventual claim; enrollment readiness for that location; the estimate if it is site-dependent | Eligibility, previsit list, issued link |
| Service | The estimate, the authorization requirement, the previsit list | Eligibility, issued link |

Curtis Ames moving off Rosa's 8:15 to Dr. Osei's 3:15 is one transition on one thread. The reason is stored — "Rosa is not approved with Ambetter yet" — the previous slot is history rather than a deleted row, and the close sheet at 5:41pm could print "Curtis Ames moved off Rosa's 8:15 to Dr. Osei at 3:15, so Ambetter can be billed for him" because the thread still knew what the 7:45am page had said.

### Governance on every transition (FD-5)

Every transition records its date, its actor, the fact that justified it, and **the tier that governed it** — because undo needs to know what it is allowed to do.

| Tier | Applies to | Undo means |
|---|---|---|
| 1 — do + undo | Most station changes: matching a fax, marking arrived, posting a charge, attaching a remit | Append the reverse transition. Silent, immediate |
| 2 — do + notify | Automatic advances: an ERA posting, an auto-release from a hold, a batch settling | Same as tier 1, and the original appears in Today and in the close |
| 3 — delay + cancel window | Anything leaving the building: claim batches, statements, outreach, records responses | Cancel before the countdown expires. After it expires, undo is a void or a replacement — slower, visible, sometimes impossible |
| 4 — hard stop | Refunds leaving the practice, material adjustments, record merges | Confirmed by a human before the fact; reversal is a new dated transaction |

**Reversibility is not rewind.** Once a claim has left the building, tier 3's countdown *is* the undo; after that the honest move is `claimed → claimed` with a replacement. The contract stores, per transition, whether the effect left the building — that single flag is what lets the undo surface tell the truth about what it can still take back.

**Every gate carries the deadline escape.** A tier-3 or tier-4 gate that would push a thread's clock past zero releases at tier 3 with notification, because a gate that destroys the money it protects is not a control. The thread is the only object that can enforce this, because the thread is the only object that holds the clock.

## Non-visit threads

First-class, enumerated, and closed — a new type is a contract change, not a configuration. Each declares the subset of the universal stations it uses, and what its four remaining properties mean.

**1. Credit balance.** Money we are holding that is not ours. Minted at `remitted` when an overpayment, an unidentified receipt, or a patient credit is recognized. Carries a **cause** — unidentified receipt, patient credit, or payer overpayment — because the cause determines the resolution and the clock. *Station:* `remitted → banked → closed`. *Clock:* the refund obligation for the cause (the federal sixty days on a payer overpayment; the state's escheatment date on an unidentified receipt). *Dollars:* the amount, direction **outbound**, counterparty the party it belongs to. *Owner:* whoever will resolve it. Page 3's "Money in, we don't know whose ... 3 ... 12 days ... $412.00" is three of these.

**2. Unmatched inbound page.** Every page that arrives is a thread from the moment it lands (C-2), before anyone knows what it is. *Station:* `intent`, and its dispositions are the interesting part — it may attach to an existing thread and `close` with a link, or it may *become* the visit thread by moving `intent → booked`. Della Frank's referral did the second. *Clock:* the sender's expectation, or a shipped default that keeps a page from sitting in the pile for a week. *Dollars:* usually zero; a scanned check is not. *Owner:* whoever is at the desk that morning, until dispositioned.

**3. Inbound page, not ours.** The misdirected-PHI case, which is not trash and has a legally required disposition. *Station:* `intent → closed`. It never links to a patient and never joins another thread. *Clock:* the notify-and-destroy window. *Dollars:* zero. *Owner:* the manager. Its `closed` transition stores the evidence — sender notified at 4:47pm, shredded 4:50pm — because that evidence is the only reason the type exists.

**4. Recoupment case.** A payer wants money back. Minted from the Pile page the demand letter arrived on — the same thread, dispositioned. *Station:* `intent → remitted → banked → closed`, with `disputed` applied while appealed. *Clock:* the decide-by date on the letter; Gilbert Ash's was Sep 24. *Dollars:* the demanded amount, direction outbound, counterparty the payer. *Owner:* the biller. It **links to** the visit thread it recoups and to the Money Line where the offset eventually lands, which is how the close sheet's Tuesday letter and the money page's short Wednesday wire became one story.

**5. Payer records request.** A payer asked for documentation. *Station:* `intent → claimed` (our answer left the building) `→ remitted` (acknowledged) `→ closed`. *Clock:* the payer's response deadline; Aetna's was Sep 8. *Dollars:* **borrowed** — the sum of the claim dollars the request protects, because a records request with no dollar figure will never be prioritized against work that has one. *Owner:* the manager.

**6. GFE dispute.** A self-pay patient disputes a bill against the Good Faith Estimate we issued (FD-4). *Station:* `intent → claimed → remitted → banked → closed`. *Clock:* the patient–provider dispute resolution window. *Dollars:* the disputed difference. *Owner:* the manager. It applies `disputed` to the visit thread it references, which suppresses that thread's statements until it resolves.

**7. Old-system migrated balance (FD-9).** One thread per open legacy balance, so the owner's money view shows whole-practice truth and the run-off is watched work rather than invisible work. Minted at the station its legacy state maps to — `claimed` where a claim is outstanding, `remitted` where a balance remains — and stamped with a **provenance boundary**: history before the migration date is one line, "brought over from MediTouch on Sep 1, balance $___." *Clock:* the legacy filing or statute date where known, empty where not. *Dollars:* the open balance. *Owner:* whoever runs it off — Marcy, Thursdays. **Its only advancing move is `closed`.** A migrated thread is never claimed, rebilled, or appealed from openPractice in the beta; converting one into a working thread is an explicit adopt transition, and the beta does not ship it. Page 3's "Still in MediTouch, the old system ... 214 ... 411 days ... $14,206.00" is 214 of these, and the row exists so the $31,824.40 total is the truth rather than a flattering subset.

**8. Enrollment case.** A provider × payer × location approval in flight. *Station:* `intent → claimed` (application submitted) `→ remitted` (the payer answered with an effective date) `→ closed`. *Owner:* the manager. Its dollars and its clock are **derived from the threads it holds** — Rosa's Ambetter case shows $2,438.40 and 53 days because that is the sum and the nearest deadline of the seven claims it is holding, not because anyone typed a number. When it reaches `remitted` with a backdated effective date, its release condition resolves and every eligible held thread advances in one action; the ineligible remainder is flagged for self-pay, appeal, or write-off rather than left behind. This is the highest-dollar administrative behavior in the packet, and it is one query because the hold reason was stored structurally.

**A gating thread's dollars and clock are always derived from what it gates.** That rule is what makes an enrollment case, a records request, and a recoupment sortable in the same list as a denied claim.

## Provenance as renderings (FD-6)

**The thread's history *is* the provenance.** There is no provenance viewer, no separate trace store, and no AI decision ledger — the review's judgment was that a two-to-four person practice never calls a trace API and wants the answer to "why does this say that" in one click, roughly twice a year. What justifies the storage is the artifact it emits.

**The contract rule: anything stored on a thread must be named by the rendering that consumes it.** If a field cannot be placed in the third column below, it is not stored. Conversely, a rendering may not be built on a field the thread does not carry — the field and the rendering land in the same change, or neither does.

| Rendering | Who opens it, when | Therefore the thread must carry |
|---|---|---|
| **The appeal packet** | The biller, on a denial worth fighting | The eligibility answer as received with its timestamp; the authorization number, units, and expiry; the charge as coded; **the claim exactly as submitted**; the acknowledgment; the remittance line with its codes; the reference to the clinical fact and who signed it |
| **Secondary-claim autofill** | The biller, minutes after posting a primary | The posted primary's allowed, paid, adjustment codes, and patient responsibility; the coverage order in force on the date of service |
| **The retro-rebill list** | The manager, the day an enrollment backdates | The hold reason as a **structured** condition — provider × payer × location × date range — never as free text |
| **The stuck-money worklist** | The biller daily, the owner weekly | The five properties kept current, plus the shipped dwell for each station |
| **"What happened to my bill"** | The patient, from a texted link, no account | A **plain-language label on every transition type**, authored once by a human, never generated per thread |
| **Undo** | Anyone, inside the window | The governing tier, the compensating action, and whether the effect left the building |
| **The close sheet and the binder** | The manager at 5:41pm; an auditor once a year | Today's transitions, indexed by day, so the evidence is the exhaust of a close that had to happen anyway |

Two of these deserve emphasis. The **plain-language label** requirement pushes staff language down into the contract rather than leaving it to a UI layer: there is one sentence per transition type, written once, and it is what both the patient's page and the close sheet print. And **undo is a provenance artifact** — which is what makes FD-5 and FD-6 one architecture rather than two. A chain built for undo must be complete in a way a chain built for audit need not be, and staff feel undo weekly and audit annually.

### What the thread freezes

The thread is an index over facts that live elsewhere, with one exception: **it freezes what a third party will judge us on.** The claim exactly as submitted, the estimate exactly as delivered, the remittance line exactly as received, and the GFE exactly as issued are copies, because the appeal, the dispute, and the audit are arguments about those documents and a reference that can drift is not evidence. Everything else — the patient's address, the payer's current name, the provider's NPI, the price today, the roster — is referenced, so that correcting a fact once corrects it everywhere and does not falsify history.

## Versioning and consumer discipline (FD-7)

The contract is versioned, starting at `visit-thread/1.0`.

- **Every thread is stamped at mint** with the contract version it was minted under. The stamp never changes. History is immutable (FD-3), so a version migration of existing threads is not merely expensive — it is prohibited.
- **Every consumer declares the version it reads.** A rendering, a surface, a connector, or an export states `reads visit-thread ≥ 1.x`, and the declaration is checked, not documented.
- **No consumer writes a station.** Transitions are made through the thread; the field is never assigned. Each transition type has exactly one declared writer.
- **No consumer enumerates stations exhaustively without a default branch.** A reader that switches on station must have an "anything else: show it, do nothing" arm. This one rule is what makes most evolution additive in practice.

**Additive (minor bump, no consumer change required):** a new optional facet; a new hold reason; a new non-visit thread type; a new link type; a new rendering; new dwell defaults; new or improved plain-language labels; a new legal transition between existing stations.

**Breaking (major bump):** removing or renaming a station; making a previously legal transition illegal; changing what the dollar amount means at a station; changing what `owner` refers to; changing the minting rule (what counts as one intent); changing how the clock selects its deadline.

**What a breaking change costs.** Because threads keep their mint stamp and history is never rewritten, both versions must be readable *forever*. A major bump is therefore not a migration; it is a permanent tax on all seven renderings, each of which must render every version ever minted — the appeal packet for a 2027 denial on a thread minted under 1.0 must still assemble. The budget follows from that arithmetic: **at most one breaking change before beta exit, and none afterward without a named deprecation, a stated reason, and a version the renderings will carry for the life of the product.** The reason this document is written before any consumer is that a decision taken now costs a paragraph and the same decision taken in month nine costs seven renderings.

## Interfaces and boundaries

**Consumes.** Intent events from booking, the walk-in path, the waitlist, and the Pile (fax, upload, scan, logged call). Party references from the party register. Roster references for owners and actors. Promises for readiness and for every clock — coverage, authorization, enrollment, price, the GFE issued, the payer's filing window, the learned baseline (FD-2). Money Lines for `banked`. Remittance and EOB lines for `remitted`. Clinical facts, by reference, for `seen` and `charged`.

**Emits.** Station, condition, age, clock, dollars, and owner. Transition events carrying the actor, the justifying fact, the governing tier, and whether the effect left the building. The re-association key set. The seven renderings.

**The party and place register is presupposed substrate (A-4).** The thread *references* the patient, the guarantor, the household, the subscriber, the referring office, the payer, and the location. It never models them. Hector Ruiz's disconnected phone number, Grace Okafor's guardian, a duplicate merge, a preferred language, a minor turning eighteen — none of these are thread facts, and the acceptance test is accordingly two-pronged: a feature must name which of the six nouns it touches **or** which party fact it establishes. The thread's obligation to the register is narrow and firm: **a merge of two parties re-points every thread and orphans none**, and because a merge is a tier-4 action with a reversal window, the re-pointing is itself a dated, reversible transition on each thread it touched.

**The Roster is likewise presupposed.** The owner is a roster reference. Marcy at Ledgerline is a roster entry with bounded access because she does the practice's work under its authority (FD-1); the interpreter vendor and Ambetter's enrollment department are not, and appear as promise makers, counterparties, and link targets.

**The clinical boundary: stations may wait on clinical facts; clinical truth stays in the EHR.** The waiting state is a `held` condition, not a station:

- Ellery Dodd's Monday visit sits at `seen`, held, reason *waiting on a clinical fact*, owner Dana, clock **inherited from the money** — date of service plus the payer's filing window. That inheritance is the point: the clinical wait is invisible to the EHR's own worklists, so if it did not carry a money clock here it would age toward a filing limit and vanish from both systems.
- The thread stores what fact it needs, whether it has arrived, when it arrived, and who signed it. It never stores the note.
- **The correction loop runs forward.** A corrected date of service, diagnosis, or provider arriving from the EHR after the claim went out does not rewind the thread; it enters as a dated event citing the EHR as its source and drives `claimed → claimed` (replacement) or `remitted → claimed` (corrected claim). The appeal packet then shows both the fact as originally submitted and the correction, which is the only honest rendering.
- **Authority is declared, not implied.** openPractice owns the thread and the administrative appointment; the EHR owns the encounter and every signed clinical fact. Where the EHR also keeps a calendar, the thread references its appointment id and mirrors its time — but a station never arrives from the EHR. Only facts do.

**Re-association, when a partner strips the identity.** The clearinghouse will not carry our thread id. The thread therefore publishes a **key set** — payer, member id, date of service, rendering provider, billed amount, and the payer claim control number once known — and inbound artifacts match against it. Three rules: a match is a dated transition with a recorded confidence and is undoable at tier 1 for thirty days; **an artifact that matches nothing mints an unmatched inbound thread and is never silently attached**; and an artifact that matches more than one thread goes to the Pile with both candidates named. The "unknown" state has a home, which is the entire reason the Pile held as a station rather than a container.

**Links, not children.** Threads reference each other by named link type — *spawned from*, *recoups*, *offsets*, *replaces*, *merged into*, *held by*. There is no parent/child inheritance and no cascade. Walter Fenn's rebooked Sep 8 injection is a **new** thread linked *spawned from* the Aug 25 visit, because it is a new intent; Gilbert Ash's recoupment is a separate thread linked *recoups*; Rosa's seven held claims are linked *held by* one enrollment case. Inheritance would have made every question about a thread a question about a tree.

## The work model

**Work is a thread stuck at a station longer than its clock allows.** That is the whole definition, and it has three consequences the packet has been missing.

**Queues are saved filters over threads, not objects.** The review counted seven proposed worklists in one cluster and six source domains firing into one list. There is one list, filtered. "Denied, nobody has worked it yet" is `station = remitted AND answer = denial AND stuck`. "Patient owes us, past first statement" is `station = remitted AND direction = inbound AND counterparty = patient AND age > first statement`. "Charged, claim held" is `station = charged AND condition = held`. Page 3's six rows are six filters over ten stations — the vocabulary needed no extension to produce them, which is the strongest available evidence that ten is enough.

**A queue cannot disagree with a metric,** because the metric is `count(filter)` and the queue is `list(filter)`. The review's complaint that "denial rate reports 12% while the denial queue holds four items" becomes structurally impossible.

**Snooze preserves age.** "The payer said call back on the 12th" sets the clock forward and leaves the age untouched, with a reason. Without it, staff close valid work to clear red and every aging number is a lie within a month.

**Dwell is shipped, never authored.** Each station carries a default dwell — how long a thread may sit there before it is stuck — derived where possible from a fact the system already holds (the payer's own median days-to-answer from promise memory beats any SLA anyone would type). The practice does not configure it.

### The six surfaces are views over threads

| Surface | What it shows | Filter, in one line |
|---|---|---|
| **Today** | Today's threads sorted by readiness, each with the named blocker and its one-click fix; what is stuck by dollars × age; yesterday's close status | Threads whose station or clock touches today, plus stuck threads with an owner |
| **The Patient** | This person's threads across time, plus their party facts and promises | All threads referencing this party or household |
| **The Thread** | One thread as a vertical story, top to bottom: station, age, clock, dollars, owner, next action — and the source of the appeal packet, the patient's page, and undo | One thread, all transitions |
| **The Money** | Did it come in; did they pay what they usually pay; where is our money sitting; does it tie | Threads grouped by station and condition, summed by dollars — plus Money Lines |
| **The Practice** | The roster, what expires, what changes when, the binder — readiness in negative form | Threads owned by promises about to change, plus the promises themselves |
| **The Pile** | Everything that arrived today, one clock and one owner per page, one disposition | Threads at `intent` of the inbound types |

The boundary between **Today** and **The Money** is a time horizon, not a subject: the same threads appear on both, actionable today on one and rolled up on the other. Saying so is what stops them being built twice.

## Naming

**"Thread" is an internal name and does not ship.** It fails the review's own acceptance test — a screen whose name the office manager would not say out loud is a screen she will not open.

The finding from three pages of paper is that **staff never name the object at all; they name the subject and the station.** "The held claims." "The unsigned notes." "Bernard's balance." "Della's referral." "The Medicare letter." Twenty-four appointments, twenty-three dispositions, six pile pages, and six money rows were printed on three sheets without the abstract noun appearing once. The contract should take that literally:

- **The UI names the subject and the station**, and does not name the class. "Curtis Ames — Tuesday 8:15 with Rosa." "Aetna wants records on three claims — due Sep 8, Dana."
- **Where one word is unavoidable** — a browser tab, an undo confirmation, the patient's own page — use **"visit"** for visit threads and **"case"** for the non-visit types. Both are words staff already say, and "case" is already the packet's word for a recoupment case and an enrollment case, so the non-visit enumeration reads naturally rather than as jargon.
- **The collective is "what's open"**, not a count of a noun. "Everything above is finished, or has a person's name and a date on it" is the sentence that closed Tuesday, and it names nothing.

Whether the third surface's tab should read *Visit*, *Case*, or *Open items* is genuinely open and is listed below; the beta practice's own search box is the right way to settle it.

## Alternatives and tensions

**One thread per visit-intent versus one per claim.** Per-claim matches the X12 world and would make the money half trivially monotonic; it also loses the patient's story at exactly the moment a patient asks for it, and it cannot hold a waitlist entry, a cancelled appointment, or a referral that never booked. Per-intent was taken, and the price is paid honestly: stations repeat, and a visit that generates a primary, a secondary, and a patient balance walks `claimed → remitted → claimed → remitted → banked`. Every consumer must therefore read the history rather than the position when the question is "what happened," and the position only when the question is "what now."

**Minting at intent mints threads that die.** Waitlist churn, referrals that never book, and cancellations mean the practice may create meaningfully more threads than it has visits, and a naive count of threads will not equal a count of visits. That is the cost of Page 1 having something to sort and Page 2 having something to dispose, and it is cheap now and expensive later.

**Held as a condition versus held as a station.** Condition preserves the position underneath, which is what makes retro-release a query and what let the close sheet print "charged, claim HELD." The cost is a second axis every consumer must read, and the discipline that no rendering may show a position without its condition — a thread displayed as "charged" when it is actually held is a lie the staff will catch once and stop trusting.

**Index versus record.** The thread is an index over facts owned elsewhere, except where a third party will judge us on the artifact. That line will be argued at every seam, and the failure mode in both directions is real: freeze too much and a corrected address falsifies a hundred threads; freeze too little and the appeal packet cites a claim that no longer says what it said.

**A fixed station vocabulary at a fixed staff size.** Ten stations and a closed hold list follow FD-1's frame rule, and they will strain first where the money path is genuinely different — workers' compensation, auto and liability, non-visit revenue such as chronic care management, and patient financing settlements. The frame governs defaults and surfaces, not data structures, so these should extend the type enumeration rather than force a station rewrite; but the strain is predictable and should be watched rather than denied.

**The clock shows one deadline.** Sorting by the nearest deadline hides the second one behind it, and a thread with two deadlines nine days apart will look calm and then abruptly will not. The alternative — showing every deadline — produces a page nobody reads. The mitigation is that the runner-up is retrievable, not that the display is complete.

**Undo cannot always be undo.** Tier 3's cancel window is the real undo for anything leaving the building, and after the countdown expires the honest answer is a void or a replacement. A product that promises undo and delivers a replacement will be caught; the contract stores whether the effect left the building precisely so the undo surface can say so plainly.

## Open questions

- Should the third surface be labeled *Visit*, *Case*, or *Open items*, and does the patient-facing page use "your visit" or "your bill"? Settle by watching what the beta practice types into search rather than by argument.
- When the EHR also owns a calendar, which system is authoritative for the appointment time — and what does the thread do during an EHR outage, when facts stop arriving but the day proceeds anyway?
- Does an episode need a thread above the visit threads — a course of physical therapy, a global surgical period, a maternity package — or is an episode a saved filter plus a link type? A filter is the cheaper answer and may not survive a global period's billing rules.
- What are the shipped dwell defaults per station? "Too long at `arrived`" is a real signal and nobody knows the number yet; the beta should measure before it ships a threshold.
- How long does a no-show thread stay open for recall before it closes, and does closing it lose the recall or hand it to a different object?
- What re-association confidence auto-attaches an inbound artifact versus mints an unmatched page? Too high and the Pile floods; too low and money attaches to the wrong visit, which is the worse error.
- Does a migrated balance thread ever become a working thread, and if the beta practice asks for that adopt transition in week three, what does it cost?
- Retention and required deletion against an append-only history: state retention limits, minors' rules, and patient-requested restrictions all require removing content from a structure defined as immutable. Decide the tombstone or chain-safe redaction shape now, before the history is a year deep.
- How long are two contract versions served side by side, and who is allowed to declare a consumer retired?

## Relationships

Written first per [Framing Decisions](../review/openpractice-framing-decisions.md) FD-7, and governed throughout by FD-5 (governance tiers), FD-6 (provenance as artifacts), FD-3 (always-closed books), FD-2 (learned baseline as the source of expected dollars and dwell), FD-4 (the self-pay and GFE money path), FD-1 (the 2–4 staff frame and the external biller as a roster entry), and FD-9 (the old-system balance thread). Implements amendments A-2, A-3, and A-4 and clarifications C-2 and C-3 from [Paper Prototypes](../review/openpractice-paper-prototypes.md), and adopts its maker/owner naming fix. Resolves the four-spine collision diagnosed in [Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md) §2(b), §4(k), and §6.5, and supplies the object the "verdicts without valves" table in §2(e) was missing.

Precedes and is bound by the Tier-1 atomics still to be written: The Day (huddle, close, and rhythm), Money Lines, Enrollment-Gated Billing, Secondary And Corrected Claims And The Deadline Control Plane, Phone/Fax/The Inbound Pile, Day One (migration and progressive activation), The Roster, and Self-Pay/Time-Of-Service/GFE. Readiness is a computation over promises evaluated against this contract, not an identity; the party and place register is substrate this contract presupposes and does not model. No existing document is modified by this contract; the documents that bind to it are revised in Wave 4.
