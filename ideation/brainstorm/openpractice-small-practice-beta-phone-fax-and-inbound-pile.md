# Phone, Fax, And The Inbound Pile — Brainstorm

Status: brainstorm
Kind: report
Summary: the two channels a small practice actually runs on are a phone that rings all day and a fax machine that is how new patients arrive, and neither was owned by any atomic — so this document makes the call a booking surface rather than a note, requires a two-tap disposition on hang-up whose cheapest answer is a deliberate nothing with a reason, makes the Pile one door where fax, voicemail, portal message, paper mail, walk-in document, and inbound photo each become a thread at `intent` on arrival and leave the moment they have a disposition, decomposes the two-minute fax-to-booked demo into exactly two station changes on one thread, gives misdirected PHI a notify-and-destroy disposition whose evidence is the only reason the type exists, refuses any outbound page to a number nobody confirmed, and states the manual-connector principle that puts the human step for a partner with no API inside the spine as a tracked station with an owner, a clock, and an artifact rather than outside it as a workaround nobody measures.
Topics: openpractice-small-practice-beta, phone-fax, inbound-pile
Repository context: openPractice — Tier-1 atomic for the small ambulatory practice beta; Slice 1, "The door"
Captured: 2026-08-27

## Possible feats

- **The call is a booking surface, not a note** — the phone rings, the caller resolves to a person, their page is already open, and what is said becomes the thread as it is said; phone-first booking is the primary booking flow at this practice size, and self-service is the convenience layered on top of it.
- **Every call ends in a disposition, including a deliberate nothing** — two taps on hang-up across two axes, *did we reach them* and *what is owed now*, with "answered, nothing owed" as a first-class one-tap outcome, because a required disposition with no cheap way to say *nothing* trains staff to lie within a week.
- **One pile, one clock, one name on every page** — fax, voicemail, portal message, paper mail, a scan of the sheet a walk-in hands across the counter, an inbound photo of an insurance card: each becomes a thread at `intent` the moment it lands, with a station, an age, an owner, and a clock, and each leaves the Pile the instant it has a disposition.
- **The fax that books itself** — recognized referral, proposed match, pre-filled registration corrected by the payer rather than proofread by a human, readiness, and a booking, all inside the page preview: two station changes on one thread and no value typed that arrived on paper.
- **"Not ours" is a disposition with evidence, not a trash can** — the misdirected page is an enumerated thread type whose closing transition stores who was told and when the copy was destroyed, because that evidence is the only reason the type exists and a trash button cannot produce it.
- **No page leaves the building to a number nobody confirmed** — outbound destinations become sendable only by verification, every transmission files its confirmation and its honest limits, and the same principle governs the texted link: the address is confirmed, the content is a link, and the send is provable.
- **The manual connector is a station, not a workaround** — where a partner has no API, the human step lives inside the spine as a tracked transition with an owner, a clock, and an artifact, and its minutes are counted, so temporary labor cannot quietly become permanent hidden labor.

## Focus

What has to be true for a phone that rings eighty times a day and a fax machine
that is how most new patients arrive to become tracked work with a station, a
clock, and a name on it — without asking the person holding the handset to do
anything she is not already doing, and without building a telephony platform to
get there?

## Current state: the dominant channel is invisible, and the second one is a pile on the counter

The feature-normalization revision states the defect plainly: the beta overview's
system diagram promises `staff · telephone · responsive web · kiosk · future app
· fax/API`, and **no atomic owned telephone or fax**. FD-10 ratified the fix and
made both Tier-1. This document is that ownership.

The two reviews found the same hole from opposite ends.

**The front-office review found a cluster with no phone in it.** Fifty-three
feats across seven documents, and its intuitiveness assessment names *"on the
phone, booking"* as the highest-pressure interaction in the practice — someone
is waiting while the receptionist decides type, provider, duration, and whether
the patient is cleared — served by a constraint engine that, as written, *adds*
decisions rather than removing them. Its communications-accountability finding is
sharper still: **"attempts made by phone (the dominant channel) never entering
the ledger at all, which makes every accountability number wrong."** Its missing
feat list puts it third, behind only the day cockpit and the walk-in:
*"Phone Console And Call Disposition — the phone is the dominant channel for a
small practice and is invisible across the entire cluster."* The walk-in path is
in the same list for the same reason, and it enters through this door too: a
patient standing at the desk holding a piece of paper is an inbound page and a
minted intent at the same moment.

**The practice-administration review found the fax and asked for less, not
more.** Its verdict on multi-channel referral intake is *differentiator, if
scoped to fax* — "a specialty practice's referral intake *is* a fax machine and
an email inbox, and the practices that lose money lose it here," while
"structured API events, secure messages, faxes, uploads, and staff entry is five
connectors; beta needs one and a half." Its unmatched-intake verdict supplies
this document's shortest sentence: **"One pile, one clock, one name on every
page."** Its gaps are the specification: *no handling of misdirected PHI — a fax
for another practice's patient arrives regularly and has a specific
disposal-and-notify obligation that a "trash" button does not satisfy*; *no
treatment of duplicate faxes, the single most common intake noise*; *no path for
a partially relevant forty-page packet*; and, on the outbound side, *fax delivery
is unacknowledged in practice — a transmission report is not a receipt — and the
outbound path needs a confirmed-fax-number registry per sender*, because **a fax
to a wrong number is a breach**. Its missing-feat list names the registry and the
misdirected-PHI disposition by name. And its cross-feat gaps name the fastest
possible referral disposition, which nothing currently connects: *"Do we take
this insurance for this service with this provider?" should be answerable in the
first thirty seconds of a fax landing.*

**The paper prototypes then proved the shape on paper before anything was
built.** Six pages arrived in Tuesday's tray — a referral fax, a payer records
request, a fax for someone else's patient, a Medicare demand letter, a plan-change
notice, and a scanned insurance card — and every one of them became a thread on
arrival and left the Pile the moment it had a disposition. The verdict records it
as the packet's hardest behavior to get right and the one that held perfectly:
**the Pile is a station, not a container** (clarification C-2). Della Frank's
referral landed on Aug 20, sat unmatched at `intent` for five days, appeared on
Tuesday's huddle page as *"her referral faxed in Aug 20 and was never matched to
her chart"* with the fix *"it is page 3 in today's pile"*, and became the visit —
it did not become a second object. The one page the enumeration could not hold
was *"Fax, not our patient"*, recorded as strain S-4 and resolved into a new
thread type whose entire justification is the line the close sheet printed at
5:41pm: **"sender told, shredded 4:50pm."**

Everything below is the channel and the door. It is not the referral, the
document envelope, or the day's rituals; those have owners, and the boundaries
are drawn explicitly under *Interfaces and boundaries*.

## Proposed feature set

### 1. The phone console: what happens when it rings

**Two tiers, and the lower one must work with no telephony integration at all.**
This is the first design decision and the load-bearing one. If the console
requires a CTI integration, the beta blocks on a partner it has not chosen, and
FD-8 has already put two partner-paced enrollments on the critical path. So:

- **Connected.** The practice's existing VoIP or fax-over-IP provider posts an
  inbound-call event with a caller number. The number resolves against the party
  register and the page opens before the second ring.
- **Unconnected.** One keystroke from any screen opens *I'm on a call with ___*
  with the search field focused. Three characters of a last name and the page is
  open. This is the fallback, and it must be fast enough that a practice with no
  integration never feels it is using the degraded path.

Both tiers produce the identical object and the identical disposition. Screen-pop
is an accelerator, not a capability.

**What the pop opens is The Patient, not a call form.** One page indexing the
person across threads, promises, and money lines: who's involved, what's true,
what's owed, what's been said, what's next. The receptionist does not navigate
during a call; whatever she needs is on the page that opened.

**Capture-as-you-talk means the thread is minted during the call, not after it.**
A thread is minted at intent (A-3), and the intent forms mid-sentence — *"can I
get in next week?"* mints a thread before a slot exists. The consequence is that a
call that ends without a booking still leaves something: a waitlist entry at
`intent`, a callback thread with a clock, a message with an owner. It never
leaves nothing by accident. Nothing is ever saved twice, because nothing was ever
held in a draft.

**The household case is where screen-pop is wrong, and it must be wrong
visibly.** One phone number for two patients is ordinary, not an edge case. A
number resolving to more than one party opens a chooser with the distinguishing
facts — who has an appointment today, who was called last, DOB — and never
guesses. A wrong pop that silently attaches a call to the wrong person is worse
than no pop, because it is invisible.

**An unknown caller is a first-class state.** No match opens a blank page with
one control: *this is a new person* / *this is about a patient I'll find* /
*this is not about a patient*. The third arm is how a solicitation, a payer, or
a referring office's coordinator gets a disposition without a party record being
created.

### 2. Disposition on hang-up, in two taps

**Two axes, because the two questions are genuinely different.** The first is
*did we reach them*, which feeds contact health. The second is *what is owed
now*, which mints or moves a thread. Collapsing them into one list produces
either twenty values or a lie.

**Axis one — did we reach a person.**

| Value | What it feeds |
|---|---|
| Reached them | nothing; the ordinary case |
| Voicemail left | the callback expectation, and a second attempt's clock |
| No answer, no voicemail | attempt count on the thread |
| Voicemail full, or no voicemail box | contact health, degraded |
| **The number is bad** | **the contact-health list, and a badge that fires at the next check-in** |

The last row is the one that pays. The fixture's close sheet reads *"Hector Ruiz
— called 9:30, the number is disconnected. Marked bad contact. Letter goes with
tomorrow's mail. Tomas."* One tap produced a durable fact, a work item, and an
owner. The front-office review's reframe governs it: *maintain, at all times, a
short list of the patients we currently cannot reach — and fix it while they are
standing in front of us.*

**Axis two — what is owed now.** Six values, closed, no authoring:

| Disposition | What it does to a thread | Tier |
|---|---|---|
| **Booked or moved** | mints a visit thread, or moves one: `intent → booked`, `booked → booked` (reschedule), or a cancellation disposition | 1 |
| **Money** | a payment taken, a pay link sent, or a plan agreed; the money path owns what happens next | 2 for a posting; 1 for a link |
| **A message for a named person** | mints a thread at `intent` with that person as owner and a shipped clock — never an unowned note | 1 |
| **We owe them a call back** | mints or moves a thread with a *by when*, which is a clock and not a hope | 1 |
| **Answered — nothing is owed** | mints nothing; records the call and its reason | 1 |
| **Not for us** | closes with a reason: wrong number, solicitation, another practice's business. If PHI was disclosed to us, it escalates to §6 | 1 |

**The fifth row is the whole design.** A required disposition whose cheapest
honest answer is expensive is a disposition that will be defeated — every call
will be filed as one value inside a month, and the resulting numbers will be
worse than no numbers. *Answered — nothing is owed* is one tap, offers a
four-item reason list (*they asked a question and I answered it* / *they were
checking a date* / *they were confirming* / *other, one line*), and is expected to
be a large minority of calls. **A taxonomy where ninety percent of calls land on
one value is a broken taxonomy, not a busy practice**, and the distribution is
the metric that says which it is.

**Defaults are pre-selected from what happened during the call.** If a booking
was made while the caller was on the line, *Booked* is already chosen and the
disposition is one tap on axis one. The staff member confirms; she does not
classify.

**Named valve (FR-3).** The disposition is not a report. It is what mints or
moves the thread, and what feeds four renderings the practice already reads: the
huddle page's morning call list, the *patients we can't reach* list, the
confirm-first list a third missed visit puts someone on, and the close sheet's
account of who was called and what came of it. The fixture's *"Naomi Frisch —
called 2:10, voicemail, rebook text sent. Third missed visit in a year"* is three
dispositions and a statistic, and no note.

### 3. Phone-first booking is the primary flow

**Say it as a decision, because it inverts the industry's direction.** At two to
four staff, self-scheduling is phone *deflection* — real value, and correctly
graded a valuable-enhancer rather than a differentiator. The bottleneck is not
that calls exist; it is what happens *during* them. So the booking flow is
designed for a person who is listening to a human being, and the web flow is
derived from it rather than the reverse.

That has four concrete consequences:

- **Three things in one frame, always: who is this, what can I offer, will it be
  billable.** The receptionist should never open a second screen while someone is
  waiting on the line.
- **Readiness is consumed here as a warning, never as a block.** The standing
  rule is *hold the claim, never the patient*. Booking against a red readiness
  verdict is not an error state; it is the evidence artifact, and the claim hold
  in Slice 3 is the valve that makes it safe. Curtis Ames is the fixture: *"Rosa
  is not approved with Ambetter yet, so we cannot bill them for her work"* and the
  fix is *"move him to Dr. Osei at 3:15"* — a readiness verdict resolved by a
  booking decision on one thread, printed on the close sheet that evening.
- **"Why can't I book this?" is a required affordance, and it must offer the
  nearest thing that would work.** A constraint that cannot explain itself to the
  person currently on the phone is a constraint that gets overridden.
- **The link goes out while they are still on the line.** *"I'm sending you a
  link now — tell me if it came through"* is the highest-converting moment a
  texted link will ever have, and it costs nothing. It also catches a bad number
  immediately, in front of the only person who can fix it.

**The walk-in enters here too.** A patient with no appointment presenting at the
desk is a mint at `arrived` — the same console, the same find-or-create, the same
compressed readiness, and the same disposition discipline. The paper on which
they arrived is a Pile page (§4); the person is a thread.

### 4. The Pile: one door, one clock, one name

**What lands.** Inbound fax; voicemail, including after-hours; a portal or
secure message; paper mail opened at the desk; a scan of what a walk-in handed
across the counter; an inbound text or photo (an MMS of an insurance card is the
common one, and the front-office review names it as having *no destination*
today); payer mail; and a check. **Scope-and-sequencing's Slice 1 text names four
of these; the surfaces table in feature-normalization names six. This document
takes the wider list** — a Pile that excludes voicemail excludes the dominant
channel's own residue, and a voicemail left at 8:40pm is the purest example of a
page arriving with nobody at the desk.

**Every page is a thread the moment it lands (C-2).** Before anyone knows what it
is: station `intent`, age running from arrival, an owner, and a clock. Dollars are
zero — except for a scanned or mailed check, which is the one inbound page that
arrives with money on it.

**The Pile is a station, not a container.** There is no Pile object, no archive,
and no "move to done". The Pile is a filter — `station = intent AND type ∈ the
inbound types` — and a page leaves it because its thread moved, not because
someone filed it. This is what let six pages be disposed against six owners with
six clocks on a printed sheet without any container being invented, and it is why
§5's demo needs no step for leaving.

**The dispositions are a closed list.**

| Disposition | Thread effect |
|---|---|
| **Attach to something that already exists** | close with a link — *"Anthony Pruitt's new BCBS card, entered 8:41am"* |
| **Become the visit** | `intent → booked` on the same thread — Della Frank |
| **Become a named case** | resolve the type: payer records request, recoupment, credit balance, enrollment correspondence |
| **File as a practice fact** | a BCBS plan-change notice, a payer contract, a CLIA letter: a party- or practice-register fact, then close |
| **Not ours** | §6, with its evidence |
| **Not sure** | park with a date. Snooze preserves original age and returns on the date, with a reason — because a "not sure" that resets the clock is how a pile becomes a drawer |

**Who owns a page at 2am.** The Thread contract forbids a null owner and the
Roster atomic has already recorded the collision: the Pile's default owner is
*whoever is at the desk that morning*, which is a shift and not a roster entry.
The Roster resolves it in its own favor and asks the question back. This document
supplies a candidate answer rather than restating the conflict: **the Pile's owner
is the practice, resolved to a name by a duty position the Roster projects onto a
Day**, in a fixed order — the person marked at the desk today; else the single
person on duty at that location today; else the practice manager. And the case the
Roster's question did not cover: **a page that arrives outside all working hours
is owned by the next resolved desk, and its clock starts at arrival, not at
opening.** Otherwise a Friday-evening fax is six hours younger than it is, every
Monday, forever. This is a proposal for the Roster to ratify, not an assertion
against it.

**Aging runs from receipt, not from processing.** The practice-administration
review is explicit: the referral clock starts when the page lands, before matching
and before anything. The huddle's number is one line — *oldest unworked page* —
and the review's own reframe is the acceptance criterion: **no inbound page waits
longer than a day without a name and a next step on it.**

**Duplicates are receipts, not threads.** The same referral faxed three times over
four days is the single most common intake noise. Content-addressing solves it for
free: identical bytes attach as a **third receipt event on one thread**, not a
third page in the pile. Near-duplicates — the same referral re-faxed with one page
added — surface as *this looks like page 3 from Aug 20* with a merge action, never
as a silent attach.

**Batches split, and the original still closes.** A forty-page packet where three
pages are the referral, and a multi-patient batch from one sending office, are the
two shapes that strain "one page, one thread". The rule: **the arrival is one
thread; a split spawns children linked *spawned from*, and the parent must still
reach a disposition.** The parent's disposition is usually *attach to something
that already exists* — it becomes the evidence the children point at.

**After-hours is a property of the clock, not a separate inbox.** A voicemail at
8:40pm is a thread at 8:40pm. It is on tomorrow's huddle without anyone doing
anything, which is the entire point of minting on arrival. Two rules ride with it:
the standing after-hours reply naming the emergency path is unconditional the
moment two-way SMS exists, and it applies to the voicemail greeting too; and
deterministic urgent-content escalation runs on inbound text and on transcribed
voicemail if transcription ships. This is the **Urgent-Content Safety Property**,
and it is beta-blocking because this document is what creates the exposure — an
administrative inbox that can receive clinical urgency is a patient-safety surface
the moment it exists.

### 5. The fax that books itself — the acceptance demo, decomposed

The demo is *"a fax landing to a booked billable appointment in two minutes with
zero retyping."* Here is what it is made of.

**"Zero retyping" is testable, and this is the test:** no value that arrived on an
inbound artifact is typed by a human anywhere on the path. Not the patient's name,
not the member ID, not the referring provider, not the requested service. A human
confirms; a human never transcribes.

| Clock | What happens | Thread effect |
|---|---|---|
| 0:00 | The fax lands. Thread minted, `intent`, type *unmatched inbound page*, owner resolved from the desk, clock at the shipped default, dollars zero. It appears in the Pile. | **mint** |
| 0:00–0:20 | Recognition *proposes*: this is a referral; the sender is Dr. Miller's office (from the inbound number, a cover-sheet token, or the destination registry's sender record); the subject is Della Frank; the requested service is a consult. Proposals are shown against the page image with their source coordinates. Nothing is written. | none |
| 0:20 | Staff open page 3 and confirm the match in the preview. The thread binds to the party and its type resolves from *unmatched inbound page* to a visit intent. **Same thread.** Dated, attributed, confidence recorded, undoable at tier 1 for thirty days. | **transition 1** |
| 0:35 | Registration pre-fills from the fax's own fields. Payer and member ID go straight to eligibility; **the 271 corrects the fields rather than a human proofreading OCR.** The card is evidence; the payer is the authority. | facts attach |
| 0:50 | Readiness runs and names its blockers in sentences. The thirty-second disposition lives here: *we don't take that plan for this service with this provider* is a legitimate, fast, recorded close. | none |
| 1:00 | Staff call the patient, or text a booking link constrained to the referred offering and the ready providers, from the same frame. Slots are pre-filtered by offering × provider readiness. | none |
| 1:40 | Book. The referring provider NPI rides onto the appointment and thence to the claim — one data path that retires a recurring rejection category. | **transition 2**: `intent → booked` |
| 1:50 | The closed-loop reply — *received*, and *scheduled for Sep 3* — composes to the sender's **confirmed** destination, in the format the sender can file, and sends behind a tier-3 cancel window. Its transmission result attaches to the thread. | outbound event |
| — | The page is no longer in the Pile, because the Pile is `station = intent` and the thread is at `booked`. | **no transition** |

**Two station changes on one thread, plus one governed outbound event.** The
last row is the one worth staring at: *leaving the Pile is not a move.* If it
were, the Pile would be a container, the page and the visit would be two objects,
and every question about Della Frank would become a question about which of them
to open.

**What the two minutes exclude, honestly.** They exclude waiting for the patient
to answer the phone, which is the longest segment of the real path and the reason
the constrained booking link exists as the parallel route. The demo measures the
practice's work, not the patient's availability, and the document should say so
before a reviewer says it first.

**The unhappy paths are the volume, and each is a disposition rather than a dead
end:** the patient never answers (a callback thread with a clock, and an offer-out
of the held slot); we do not take that insurance (closed with the reason, counted
— the practice needs to know how many it turns away); wrong specialty (closed,
sender told); the sender sent nothing usable (a *needs information* reply to a
confirmed destination, and the thread stays at `intent` with the clock running);
the requested provider is unavailable (booked with someone else, and the sender is
told that too, because a specialist who silently substitutes loses the referrer).

**And the speed does not come from the extraction.** It comes from the layout:
preview, match, eligibility, and open slots in one frame. If recognition is sixty
percent accurate the demo still runs in two minutes. If the frame is wrong,
perfect recognition does not save it. Design the screen first and let the object
model fall out of it, exactly as the review's *two-minute triage* alternative
proposed.

### 6. "Not ours" — the misdirected page

A fax for another practice's patient arrives regularly. It is not trash, it is not
ours to file, and the person named on it is not our patient.

**It is an enumerated thread type with two stations and no links.** `intent →
closed`. It never attaches to a patient and never joins another thread. That is a
real constraint with teeth: **the subject of a misdirected page must not become a
party-register entry.** The thread stores the sender, the arrival, the two acts,
and their times — not the person.

**The closing transition requires three things, and will not complete without
them:**

1. **The sender was told** — by the channel it arrived on, to a confirmed
   destination (§7), with minimum-necessary content: *you sent us a page for
   someone who is not our patient; we have destroyed it; here is the number you
   probably wanted.*
2. **The copy was destroyed** — the page, the image, and the transcription.
3. **Both are recorded with times** — *"sender told, shredded 4:50pm."*

That line is the artifact, and it is the only reason this type exists. A trash
button produces nothing that survives a question asked eleven months later.

**Governance is tier 2, do + notify, and the reasoning is uncomfortable but
right.** Destruction is irreversible, which normally argues for tier 4 — but a
hard stop on destroying misdirected PHI means the copy sits in the system waiting
for an approver who does not exist, and *delay is the harm here*. So it executes,
it appears in Today and in the close, and the thread's *did the effect leave the
building* flag says plainly that the undo surface cannot take it back.

**The boundary test is a relationship, not an address.** A page for *our* patient
sent to us in error by the wrong sender is ours — misdelivered, not misdirected.
A page for a patient we have no treatment, payment, or operations relationship
with is not ours. One test, applied once, at the desk.

**This is the packet's first required deletion**, and it collides with an
unresolved decision. R-6 and the Thread contract's own open question both flag
append-only integrity against required deletion, with no ratified answer. Every
other retention obligation in the packet is measured in years; this one is
measured in hours. It is therefore the cheapest and earliest real test of whatever
tombstone or chain-safe-redaction shape the ledger adopts, and it should be used
as that test deliberately rather than discovered as an exception. Recorded below.

### 7. Confirmed destinations, and everything that leaves by one

**A number becomes sendable only by verification.** Never by being typed once.
The shipped verification methods, in descending order of how much they cost the
practice:

- **It was the sending number on an inbound page we successfully matched.** Free,
  and the strongest ordinary evidence available: they faxed us from it.
- **A test page was acknowledged by a named human at the destination**, with the
  date and the name recorded.
- **Confirmed by phone with a named person on a date.** The manual path, and it is
  a manual connector step (§8) like any other — owner, artifact, minutes.

**Per referring office the registry holds** verified destinations and their
verification evidence, a named contact, the format they can actually receive, and
what they have asked to be sent. It is the **referral-source master record**, and
the sender scorecard the practice owner will eventually want is a rendering over
it — owned, per the boundary below, by the referral atomic.

**Delivery confirmation is evidence with stated limits, and the surface must say
so.** The review's line is exact: *a transmission report is not a receipt*. So the
thread stores the transmission result, and the page renders it honestly:
**"Sent and confirmed by the receiving machine at 4:12pm. This proves the page
reached that number. It does not prove anyone read it."** A product that lets a
transmission report masquerade as an acknowledgment will be caught once, and then
nothing it says about delivery will be believed again.

**Outbound governance is tier 3, delay with a cancel window**, because a fax to a
wrong number is a breach and the cancel window is the cheapest control that exists
at this staff size. Content is minimum-necessary by construction: what a referring
office needs to file a status is the patient's name and DOB, our provider, the
date, and the status — not the referral packet back again.

**No fax connector activates without a linked, dated, unexpired BAA.** The fax
service is a vendor handling PHI. This is **BAA-Gated Vendor Activation** applied
to a transport, and it is the same gate the Roster applies to the external biller.

**The texted link is the same principle at a different address type.** Three link
types ride this channel in the beta: the booking confirmation, the visit task
list, and the pay link. Three rules govern all three:

- **Link, never content.** The message says there is something to do; the page
  says what. This is what makes a shared household phone survivable and what keeps
  a lock-screen preview from disclosing anything.
- **The address is confirmed by delivery, and failure becomes work.** A hard
  bounce or a carrier block is a contact-health row, not a log line.
- **We own the sending and the proof; we do not own the deciding or the
  content.** Whether a message may leave is a consent decision owned by
  communications, and it must be structurally impossible for a send path to reach
  a provider adapter without one. What is behind the pay link — balance, why, and
  pay-now with no account — is the patient money view, owned by the self-pay and
  GFE money path (FD-10b).

### 8. The manual connector lives inside the spine

**The principle, in one sentence: when a partner has no API, the human step is a
station on the thread — with an owner, a clock, and an artifact — and never a
task performed outside the system and reported back into it.**

This is the beta's answer to a real scope-and-sequencing open question: *what is
the minimum viable form of the manual connector such that fax, paper, and
payer-portal work keep the same status surface as automated exchange?* The answer
is that there is no separate manual mode. There is one status surface, and a
manual step differs from an automated one only in who performed it.

**A manual step must produce the same three things an automated one does.** A
status, a timestamp, and an artifact. Concretely:

| Manual step | The artifact that makes it a transition |
|---|---|
| A payer portal upload | the portal's confirmation number, and a screenshot where it gives none |
| A phone call to a payer or enrollment department | the name of the person spoken to, a reference number, the date |
| An outbound fax to a payer or referring office | the transmission result and its stated limits (§7) |
| A mailed letter or check | the mailing date, and the certified receipt where one exists |
| A hand-carried document into an EHR with no ingestion API | the EHR's own reference, plus *sent to EHR, not acknowledged* as a visible station until it is |

**If it cannot produce one, it is not a connector — it is a hope.** That is the
rule a reviewer can fail a design on.

**Three properties follow, and each closes a hole the reviews found:**

- **Manual steps are counted, and their minutes are estimated.** Scope-and-
  sequencing's own tension states it: *manual fallbacks increase safety and
  launchability, but they must be measured so temporary work does not become
  permanent hidden labor.* The weekly count of manual steps by type is the
  automation backlog, ranked by minutes, generated rather than argued about.
- **Silent-failure detection applies to manual connectors too.** A manual step
  type that has emitted nothing in a week is a canary, exactly as an automated
  detector is. A fax line that quietly stopped receiving looks identical to a
  quiet week, and only the absence of expected activity distinguishes them.
- **The manual path is the outage path, and it is the same path.** When the fax
  service is down, the practice sends from the machine on the counter and files
  the transmission report as the artifact. Nothing about the thread changes. This
  is what makes the exception path testable rather than aspirational.

### The artifacts, and the minutes (FR-4)

Every provenance link here names what it emits and who touches it.

| Artifact | Who touches it, when | Minutes it saves |
|---|---|---|
| **The morning Pile sweep** | whoever is at the desk, every morning | replaces reading a physical tray and remembering what was in it yesterday |
| **The close sheet's "what came in the door today"** | the manager at 5:41pm | six lines that prove nothing arrived and vanished; the page files itself into the binder |
| **The notify-and-destroy record** | nobody, until someone asks eleven months later | the only answer to a question that otherwise has none |
| **The transmission confirmation with its stated limits** | the biller, in a referral or payer dispute | ends the argument about whether we sent it |
| **The contact-health list** | the desk, at the next check-in | fixes a bad number in front of the only person who can |
| **The closed-loop reply the sender can file** | a human in the referring office | the marketing feature disguised as an administrative one |
| **The manual-step ledger, counted by minutes** | the product team, weekly | ranks the automation backlog by evidence rather than by argument |

### Rhythm and clearing conditions (FR-5)

| Cadence | What this capability does | Clears when |
|---|---|---|
| **Day** | the Pile is swept in the morning and accounted for at the close; every call gets a disposition as it ends; the huddle prints the oldest unworked page | the close sheet's inbound section shows every page done or carrying a name and a date |
| **Week** | destinations that failed to confirm; senders who sent us pages we could not match; the manual-step count by type and minutes | each line has an owner |
| **Month** | the contact-health list worked down; the destination registry's unverified rows | nothing sendable is unverified |
| **Quarter** | fax and messaging vendor BAAs and their expiries appear on The Practice | renewed or the connector deactivates |
| **Year** | *(nothing of its own)* | — |

**Clearing conditions are structural, not clicked.** A Pile thread clears because
it moved to another station, not because someone marked it done. A call
disposition clears at the moment it is given. The only human-marked clearing in
this document is *not sure*, which parks with a date and returns — and preserves
original age, so the parking is visible rather than laundering the clock.

### Governance tiers (FR-6)

| Action | Tier | Note |
|---|---|---|
| Match a page to a party; book from a call; attach; snooze with a reason | **1 — do + undo** | the overwhelming majority; a match is undoable for thirty days with its confidence recorded |
| Post a payment taken on the phone; destroy a misdirected page; mark a number bad | **2 — do + notify** | executes at once, appears in Today and in the close; the destroy is irreversible and the thread says so |
| Any outbound fax, any outbound text or link, any closed-loop reply | **3 — delay + cancel window** | everything that leaves the building; a wrong destination is a breach and the countdown is the control |
| *(none)* | 4 — hard stop | nothing here moves money out of the practice or changes a record's identity; a merge arising from a match is tier 4 where it lives |

**Every gate carries the deadline escape.** A tier-3 countdown that would push a
records-request response or an appeal past its own clock releases with
notification rather than expiring, because a gate that destroys the money it
protects is not a control.

### Naming (FR-2)

No architecture vocabulary reaches a surface. What the screen says:

| Never on a surface | What the page says instead |
|---|---|
| inbound item, unmatched | *page 3, nobody has claimed it* |
| misdirected PHI disposition | *not our patient — tell them and shred it* |
| call disposition taxonomy | *what happened?* |
| confirmed fax destination registry | *where we send to Dr. Miller's office* |
| unmatched intake queue | *today's pile* |
| manual connector | *(nothing — a manual step looks like every other step)* |
| screen-pop, CTI, transport adapter | *(never spoken)* |

The last two rows matter most. **The manual connector must have no name on any
surface**, because the moment staff can see which steps are "manual" they will
treat those steps as second-class and stop recording them. And *the Pile* passes
both forms of the naming test: the newest person at the desk can explain it to a
patient in one sentence, and it is a phrase the office manager already says out
loud.

### What is read, not authored (FR-8)

Nothing in this capability asks the practice to author what an artifact already
knows.

- **The destination registry seeds from inbound faxes.** Every page we
  successfully matched carries the number it came from and the office that sent
  it. After a month the registry is mostly populated by arrival.
- **The sender list seeds from the practice's own prior claims.** Referring
  provider NPIs on twelve months of claim history are the practice's actual
  referral network, already documented, already authoritative.
- **The shipped clocks are read from behavior where possible.** How long a
  sender historically waits before re-faxing is a better default than a number
  anyone would type.
- **Contact facts come from delivery outcomes**, not from an audit anyone runs.

## Interfaces and boundaries

**Consumes.** Inbound transport events from the fax service, the telephony
provider, the SMS/MMS provider, the portal, and the desk scanner. The party and
place register, for the caller and subject match (A-4 — this document references
parties and never models them). The Roster, for the resolved desk owner and for
every actor on every transition (N-1). Readiness, for the booking warning and the
thirty-second referral disposition. Promises — coverage, authorization,
participation — as the facts readiness evaluates. The consent decision, for every
outbound message. The visit task list URL, for the link sent while the caller is
still on the line.

**Emits.** Threads at `intent` of the inbound types, minted on arrival with a
station, an age, an owner, and a clock. Call dispositions on both axes. A
pre-filled registration draft and an eligibility request. A booking intent
carrying the referring provider NPI. Outbound transmissions and their
confirmations, with their limits stated. The contact-health list. The
manual-step ledger with its minutes. Two sections of the day's rituals: the
morning sweep and the close sheet's inbound account.

**Named valves (FR-3).** Every verdict here names what changes when it changes:
*caller number resolves to one party* → the page opens; *resolves to more than
one* → a chooser, never a guess; *disposition given* → a thread is minted or
moved; *reach outcome* → the contact-health list and the next check-in prompt;
*recognition confidence above the bar* → a proposal; *below it* → an unmatched
page with no proposal, never a silent attach; *page hash matches a prior page* →
a receipt event, not a second thread; *unmatched age past its clock* → the
huddle's oldest-page line; *"not ours" confirmed* → notify, destroy, and the
record that is the only reason the type exists; *destination unverified* → the
send is refused, not warned about; *BAA absent or expired* → the connector will
not activate; *a manual step with no artifact* → the step is not done.

**The boundary against the referral atomic, stated once.** **This document owns
the envelope and the door; [Referral Administration](openpractice-small-practice-beta-referral-administration.md)
owns what the letter says and what is owed to whoever sent it.** Concretely — we
own arrival, the Pile station, aging and ownership before match, the match
*proposal* and the confirmation transition, the physical transport in both
directions, destination identity and verification, and the transmission evidence.
The referral atomic owns the Referral Case after the match: administrative
completeness and its checklist, the coverage and authorization work, the
*semantics* of closed-loop status (which states exist, when each is owed, what
may lawfully be said in one), multi-appointment referral series and their
authorization units, outbound referrals, sender conversion and scorecards, and
the four-way separation of identity match, administrative completeness, clinical
appropriateness, and appointment reservation. **Neither document may define the
other's half.** One consequence is recorded below: that atomic's *multi-channel
referral intake* feat predates FD-10 and should be read as superseded on the
channel question and intact on everything else.

**The boundary against administrative documents.** [Administrative Documents And
Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md)
owns the evidence envelope, the content hash, malware scanning, retention classes
and disposition, signature and version binding, and destination-aware routing to
the EHR. This document owns arrival, aging, ownership, and disposition. **One
inbound page produces one thread and one evidence object, and they are not the
same thing** — the thread carries the station, the clock, and the owner; the
evidence object carries the bytes and the retention class. Building two stores of
either is the failure mode, and the content-addressing that makes duplicate faxes
free lives on their side, not ours.

**The boundary against communications.** Consent decides whether a message may
leave, and the decision must be architecturally unbypassable. This document
decides how it physically goes and proves that it went. A send with no recorded
permission basis is a defect in their layer that manifests in ours.

**The boundary against the clinical record.** Clinical paper passes through this
practice; it does not live here. A clinical document arriving in the Pile is
routed to the EHR with a reference retained, and **"sent to EHR, not
acknowledged" is a visible station**, not a silence — a routed document that
failed to land is worse than one that stayed. Where the EHR has no ingestion API,
the hand-carry is a manual connector step (§8) with its artifact.

**The boundary against the Day.** The morning sweep and the close sheet's inbound
account are *sections of rituals owned by* [The Day](openpractice-small-practice-beta-day-rhythm.md).
This document supplies the rows; it does not own the page, the print layout, or
the signature.

**What this document does not own.** The Thread contract (it mints against it).
The party register (it references it). The Roster (it consumes a resolved name and
proposes how the resolution should work). Readiness (it consumes verdicts and
supplies one consumer). The money view behind a pay link, and the estimate or GFE
that produced the number on it. Telephony itself — no PBX, no call recording, no
IVR, no queueing, and no hold-time analytics in the beta.

## Alternatives and tensions

**Voice-last is a real alternative and it was taken halfway.** The front-office
review's own third option for unified threads was *leave telephony out of beta
entirely; log call outcomes as two-click dispositions*. That is cheap, safe, and
mostly right — and this document adopts it for the *building* half (no telephony
platform) while rejecting it for the *recording* half (the disposition is
required). The tension is honest: a disposition without a screen-pop costs the
staff member a search, and a cost paid eighty times a day is a cost that gets
skipped. The two-tier console exists precisely to make the unconnected path fast
enough that skipping is not tempting, and if it is not fast enough, the whole
capability degrades to a paper list beside the phone — which is the failure it
exists to prevent.

**The required disposition is a tax on the busiest person in the building.**
Eighty calls at two taps is a hundred and sixty deliberate acts a day. It will be
defeated by filing everything as one value long before it is defeated by refusal.
The mitigation is structural — the default is pre-selected, *nothing is owed* is
one tap, and the value distribution is itself the health metric — but the risk is
real and it should be measured in week one rather than asserted away.

**Screen-pop is confidently wrong in exactly the case that is most common.** Two
patients on one phone number is not an edge case; it is a family. The chooser is
the mitigation, and the cost is that the fastest path has a fork in it precisely
where speed matters most.

**The two-minute demo depends on recognition it explicitly distrusts.** The
resolution above — *the speed comes from the layout, not the extraction* — is
correct and also convenient, and it should be falsified rather than believed:
run the demo with recognition disabled and time it. If it still lands under three
minutes, the claim holds. If it does not, this document is quietly depending on
OCR while telling everyone it is not.

**The Pile as a station will strain first on the batch.** A forty-page packet, a
multi-patient fax, and a page that is legitimately about two threads all press on
"one arrival, one thread". Spawning children with a parent that still closes is
the answer here, and it is untested. The alternative — a container after all —
would be simpler for the batch and would cost the property that made the whole
prototype hold.

**"Not ours" requires destruction from a structure defined as immutable.** There
is no ratified answer, and this is the packet's earliest instance with the
shortest fuse. Naming it here does not resolve it.

**Building a fax capability in 2026 looks backwards, and is not.** It is the only
channel every referring office in the country can receive today, and it is how a
specialty practice's new patients actually arrive. The hedge that makes it
durable rather than nostalgic: **the destination registry is address-type
agnostic.** A confirmed destination may be a fax number, a Direct secure-messaging
address, or a portal. The registry, the verification discipline, and the evidence
model survive the channel's eventual death; only the transport is replaced.

**Phone-first booking inverts where the industry is heading, deliberately.** Every
incumbent is pushing patients to self-schedule, and at a large practice that is
correct. At two to four staff, the bottleneck is not that calls exist — it is what
happens during them. The risk is that this reads as a refusal to modernize; the
answer is that the web flow is derived from the phone flow rather than absent, and
that a booking link is texted *from inside* the call.

**Naming the manual step as a first-class station makes it comfortable, and
comfortable manual steps never get automated.** That is a genuine cost of the
design. The counterweight is that its minutes are counted and reported weekly, so
the comfort is visible and priced. If the manual-step count is flat at month six,
the principle has become an excuse.

## Open questions

- **Does a call that owes nothing get a thread — and if not, what carries its
  record?** *Answered, nothing is owed* mints no thread by definition, which
  leaves the record living in a call log that is not a thread. That is either a
  correct, narrow exception or **a seventh noun in disguise**, and the work
  model's own acceptance test says a seventh noun needs an argument rather than a
  specification. This is the sharpest unresolved question in the document. The
  candidate arguments are: (a) the call log is a party fact, which the register
  already presupposes; (b) every call mints a thread and most close instantly,
  which is honest but multiplies thread count by an order of magnitude; (c) the
  disposition attaches to the Day rather than to anything else. None is obviously
  right.
- **What resolves the desk to a name on a hard day?** The duty-position proposal
  in §4 handles a normal Tuesday and an after-hours arrival. It does not handle
  two people out with a temp at the desk, or a location with nobody on duty at
  all. The Roster owns the answer; this document owns the requirement that the
  answer exist before the Pile ships.
- **What recognition confidence proposes a match versus mints an unmatched page
  with no proposal?** The Thread contract asks the same question from the other
  side. Too high and the Pile floods with unproposed pages; too low and a page
  attaches to the wrong patient, which is the far worse error. It should be
  measured on the design partner's first two hundred faxes, not chosen.
- **What is the shipped default clock on an unmatched page?** Nobody knows the
  number. The review's implicit answer is one business day; the honest answer is
  that the beta should measure the practice's own historical turnaround before
  shipping a threshold.
- **Is voicemail transcription in scope?** It makes voicemail searchable, makes
  urgent-content escalation possible on the dominant channel, and creates a new
  PHI surface with a retention schedule and a vendor. Deferring it means the
  after-hours safety property covers text and not voice, which is the channel
  more likely to carry urgency.
- **What does the product owe an after-hours caller beyond the emergency path?**
  A voicemail box, an SMS auto-reply, and a web page are three different promises
  with three different failure modes, and the practice will be judged on whichever
  one it appears to have made.
- **Does the practice's existing fax number port?** If it cannot, referring
  offices will fax the old number for a year and the Pile will be structurally
  incomplete for that year. This is a partner-paced discovery, not a design
  choice, and it should be answered before Slice 1 rather than during it.
- **What happens to a check that arrives in the Pile?** Dollars are non-zero at
  `intent`, which the contract permits. Who owns it, what its clock is, and
  whether it can reach `banked` from the Pile without ever being matched to a
  claim are unspecified.
- **How is the misdirected page actually destroyed, in a system with backups?**
  Shredding paper is easy. Removing an image from a running system, its backups,
  and any transcription of it, while keeping the immutable record that it existed,
  is the tombstone question in its most acute form.
- **Do outbound and inbound share one destination identity?** A referring office
  is both a sender we receive from and a destination we send to. Modelling them as
  one record is obviously right and quietly assumes the number they fax *from* is
  the number they want replies *at*, which is frequently false.

### Discrepancies recorded against the binding inputs

- **The non-visit thread enumeration does not agree between the two governing
  documents, and this document sits on the disagreement.**
  [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md)
  Part 1 lists **seven** non-visit types including a distinct *unmatched
  referral*; [The Visit Thread](openpractice-small-practice-beta-visit-thread.md)
  enumerates **eight**, has no *unmatched referral* type — a faxed referral is an
  *unmatched inbound page* that becomes the visit thread — and adds *GFE dispute*
  and *enrollment case*, which the normalization list omits. Per the binding rule
  that visit-thread governs thread mechanics, **the eight-type enumeration
  governs and there is no separate unmatched-referral type**; this document is
  written against that reading, and the normalization list should be reconciled at
  its next edit. The difference is not cosmetic: a separate referral type would
  reintroduce the second object that C-2 exists to prevent.
- **The Pile's inbound scope is narrower in the slice than in the surfaces
  table.** Slice 1 of [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)
  names "fax, upload, scan, and payer mail"; the six-surfaces table in feature
  normalization names "fax, upload, scan, payer mail, inbound text, a logged
  call". This document takes the wider list and adds **voicemail** and **the
  document a walk-in hands across the counter**, and recommends the slice text
  widen to match. A Pile that excludes voicemail excludes the residue of the
  channel the slice exists to own.
- **The Pile's owner is a shift, not a Roster entry — engaged rather than
  restated.** [The Roster](openpractice-small-practice-beta-roster-access-and-offboarding.md)
  recorded the collision and asked the question back. §4 above supplies a
  candidate resolution — the owner is the practice, resolved to a name by a duty
  position the Roster projects onto a Day, in a fixed order — and adds the case
  the question did not cover: **a page arriving outside all working hours is owned
  by the next resolved desk, with its clock starting at arrival rather than at
  opening.** Offered for the Roster to ratify; not asserted against it.
- **A2P/10DLC carrier registration is a partner-paced dependency on nobody's
  critical path.** The front-office review names it as *a genuine go-live blocker
  for a small practice sending healthcare SMS* that *appears nowhere in the
  packet*. FD-8's kickoff-thread list names clearinghouse and merchant enrollment
  only. Slice 1's texted booking link and Slice 2's pay link both depend on it,
  and it takes weeks. **Recommended as a third kickoff thread**, opened on day one
  alongside the other two, with the same rule: a slip moves the date, not the
  acceptance criteria.
- **The referral atomic's channel feat predates FD-10.**
  [Referral Administration](openpractice-small-practice-beta-referral-administration.md)
  (captured 2026-08-26) lists *multi-channel referral intake* — "structured API
  events, secure messages, faxes, uploads, and staff entry into one traceable
  intake queue" — as its own scope. FD-10 moved the channel here. Read as
  superseded on the channel and intact on the Referral Case, rather than as a
  contradiction; noted so the next edit of that document can make the seam
  explicit.
- **The call disposition had a capability name but no named valve.** Feature
  normalization's FR-3 valve list enumerates twelve verdict→consumer pairs and
  does not include one for a call disposition, though the register names *Phone
  Console And Call Disposition* as beta-blocking. §2 supplies it — *disposition →
  the thread it mints or moves, and four renderings* — and it should be added to
  the FR-3 list at the next edit.

## Relationships

Owns the capability-register entries **Phone Console And Call Disposition**,
**One Inbound Pile**, **Confirmed Fax Destination Registry**, and **Misdirected
PHI Disposition** from
[Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md)
Part 3, and supplies the channel half of **Walk-In And Same-Day Access** and
**Snooze With A Reason**. Exercises FR-1 (the person who does this is at the desk
on a Tuesday morning), FR-2 (no *queue*, *intake*, *disposition taxonomy*, or
*connector* on any surface — and the manual connector has no name at all), FR-3
(thirteen named valves), FR-4 (seven artifacts with the minutes each saves), FR-5
(a day rhythm whose clearing conditions are structural), FR-6 (four tiers with one
deliberate tier-2 irreversibility), and FR-8 (the destination registry seeds from
inbound faxes and the sender list from prior claims).

Mints against [The Visit Thread](openpractice-small-practice-beta-visit-thread.md)
and is the fourth minting event in its table — *an inbound page that implies a
visit* — implementing clarification C-2 in full, using non-visit types 2
(*unmatched inbound page*) and 3 (*inbound page, not ours*), and delivering the
Pile as a station rather than a container. It is the document that Della Frank's
line in that contract was written for.

Bounded against [Referral Administration](openpractice-small-practice-beta-referral-administration.md)
by one sentence — **we own the envelope and the door; the referral owns what the
letter says and what is owed to whoever sent it** — and against
[Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md)
by another: one inbound page produces one thread and one evidence object, and
they are not the same thing. The **payer records request** that arrives as a Pile
page becomes the non-visit thread owned by
[Secondary Claims, Corrected Claims, And The Deadline Control Plane](openpractice-small-practice-beta-secondary-corrected-claims-and-deadlines.md),
which also owns the deadline escape that every tier-3 outbound gate here must
consult.

Supplies two sections to [The Day: Huddle, Close, And The Practice Rhythm](openpractice-small-practice-beta-day-rhythm.md)
— the morning Pile sweep and the close sheet's *what came in the door today* —
and consumes from [The Roster](openpractice-small-practice-beta-roster-access-and-offboarding.md)
the resolved name that every page and every call must carry, returning a
candidate resolution for the duty-position question that document raised first.
The pay link this channel sends is the delivery half of the patient money view in
[Self-Pay, Time-Of-Service Money, And The Good Faith Estimate](openpractice-small-practice-beta-self-pay-and-gfe.md),
which owns the balance, the estimate, and everything behind the link; this
document owns only the sending and the proof that it sent.

Sequenced as Slice 1, "The door", in
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md),
advancing **D-6** — *a fax and a phone call become tracked work with a clock and
an owner, instead of a pile on the counter* — and carrying the first acceptance
demo, *a fax landing to a booked billable appointment in two minutes with zero
retyping*. Governed by [Framing Decisions](../review/openpractice-framing-decisions.md)
FD-10 (which created this document), FD-1, FD-5, FD-6, FD-7, and FD-4/FD-10b for
what rides the outbound link.

Derives the phone console, the call disposition, the walk-in entry, the
desk-side exception, and the *dominant channel is invisible* finding from
[Front Office And Access](../review/openpractice-feat-review-front-office-and-access.md)
§3 (C1, C5, C6), §9 (missing feats), §10 (cross-feat gaps), and §11; derives the
fax-scoped intake, the one-pile-one-clock-one-name reframe, the unmatched intake
queue, the misdirected-PHI gap, the duplicate-fax problem, the confirmed
destination registry, and the *two-minute triage* screen design from
[Practice Administration](../review/openpractice-feat-review-practice-administration.md)
§3.1–3.5, §4.1–4.3, §7.2, and its missing-feat list. The six pages of Tuesday's
tray, the disposition lines the close sheet printed, and strain S-4 are from
[Paper Prototypes](../review/openpractice-paper-prototypes.md) Pages 1 and 2.
Consumes the outbound-message consent model from
[Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md),
the connector and manual-connector framing from
[Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md),
the booking surface from
[Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md),
and the assisted-mode and channel-parity invariant from
[Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md).
Modifies no existing document; the reconciliations it proposes are recorded above
rather than made.
