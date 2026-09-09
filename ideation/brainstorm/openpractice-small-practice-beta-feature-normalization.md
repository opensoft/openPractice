# Feature Definition And Catalog Normalization — Brainstorm

Status: brainstorm
Kind: report
Summary: the fifteen feature domains remain the strategic research map, but the beta catalog is now normalized against one work model — six nouns, one verb, and a party register the nouns presuppose — and one feature contract of eight testable rules covering frame, naming, valves, artifacts, rhythm, governance tiers, licensing, and learning; phone, fax, and the inbound pile become owned capabilities, the companion app stays deferred while the patient money view does not, and every capability the beta may sequence is registered here by name so the slices cannot reference something this document has not defined.
Topics: openpractice-small-practice-beta, feature-normalization, openpractice-feature-list, feature-roadmap
Repository context: openPractice — normalization of the existing feature-list brainstorm for a small ambulatory practice beta
Captured: 2026-08-27

## Revision note

This document was revised in place on 2026-08-27 against two binding inputs:
the eleven ratified framing decisions in
[Framing Decisions](../review/openpractice-framing-decisions.md)
(FD-1 through FD-11, all ratified 2026-08-27), and the PASS-WITH-AMENDMENTS
verdict in [Paper Prototypes](../review/openpractice-paper-prototypes.md)
(amendments A-1 through A-4, clarifications C-1 through C-3, and the maker/owner
naming fix N-1). Both derive from
[Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md), whose
§7(a) named this document and
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)
as the two that must be revised before anything else is written.

The revision changes what this document *is*. It was a catalog-shape memo. It is
now also the normative frame: the work model, the feature contract, and the
register of capability names that scope-and-sequencing slices against. Status
remains `brainstorm` and contradiction remains legal across the packet — but a
contradiction with FD-1 through FD-11 or with A-1 through A-4 is now a defect in
this packet rather than a legitimate variant.

## Possible feats

- **One work model, stated once** — six nouns, one verb, and a named substrate, so that the same computed diff is not specified three times under three names.
- **Two-level catalog** — retain broad capability domains for strategy while defining independently releasable capabilities beneath them.
- **A feature contract with teeth** — eight testable rules that a feature document either passes or fails, plus the eleven-point document checklist.
- **A capability register the slices can cite** — every beta-eligible capability named here once, so sequencing cannot invent a feature that no document owns.
- **Ownership without duplication** — transport, workflow meaning, source data, work, and metric semantics assigned to separate owners joined by contracts.
- **Decision backlog** — unresolved questions named as product decisions rather than hidden inside prose; the eleven that mattered most are now decided.

## Focus

How should the current feature research become a coherent beta catalog without
discarding its useful competitive analysis, provenance, safety boundaries, or
alternative designs — and what must a feature document prove before it may be
staged?

## Assessment of the current set

The existing [openPractice Feature List Overview](openpractice-feature-list-overview.md)
describes fifteen atomic domains across four clusters. Those atomics contain 96
`Possible feats`, and the packet as a whole preserves 63 open questions. Six
cluster reviewers subsequently graded 167 feats: 42% essential-core, 22%
questionable or deferred. That distribution says the packet is not over-scoped;
it is **mis-shaped**. Cutting the questionable fifth would leave the beta
essentially unchanged and would fix none of what the reviewers actually found.

The catalog problem is structural rather than conceptual:

- domains, cross-cutting services, delivery channels, and experimental features sit at the same level;
- several `Proposed feature set` paragraphs contain ten or more independently releasable behaviors;
- dependencies are described narratively but not represented as release gates;
- user roles and end-to-end exception states are not consistently explicit;
- success measures exist at cluster level but not as acceptance criteria for each capability;
- **there was no work model**, so one computed diff was specified as three "readiness contracts", one correlation identity was specified as four competing spines, and six independent "one screens" were designed for the same three people;
- **the dominant channels were invisible** — the system diagram promises telephone and fax and no atomic owned either;
- **the vocabulary is the vendor's, not the practice's**, in all six clusters.

The strongest existing qualities remain invariant and are not revisited here:
clinical facts stay in the connected EHR; financial and patient-impacting
authority stays bounded; automation records evidence and provenance; connectors
remain replaceable; and operational metrics use published semantics.

---

# Part 1 — The work model

## This is a work model, not a domain model

The six nouns below describe **what is happening to things**: a thread moves, a
promise expires, a day closes, money lands. They are deliberately silent about
the things themselves — who the patient is, which household they belong to,
where the office is, what a slot is, what a code costs. That silence is a virtue
**only while it is declared** (paper-prototype amendment A-4). Undeclared, it
reads as a prohibition, and the first person who needs a household or a schedule
template will either invent a seventh noun or conclude the model is wrong.
Neither is true.

The parties and places the nouns refer to — patient, household, guarantor,
guardian, subscriber, referring office, payer, vendor, bank, processor, location
and its hours, and the contact facts attached to each — form a **plain party and
place register**. It is presupposed substrate. It is not a seventh noun, it needs
no special modeling ceremony, and features that establish party facts (duplicate
merge and its aftermath, household coordination, the minor-to-adult authority
transition, interpreter and accommodation needs, a phone number that bounces) are
legitimate beta features that touch no noun at all.

## The six nouns and one verb

**1. The Day.** The unit of time and the unit of completion. A day opens with a
huddle and closes with a close; the close is what converts "in progress" into
"done and provable". Weeks, months, quarters, and years are longer days with
their own open and close. Because the books are always closed (FD-3), month-end
is printing rather than a procedure: immutability at post time, corrections as
dated reversals, as-of queries, and export-batch freeze as the only lock.

**2. The Thread.** The durable identity everything attaches to, and the
resolution of the four competing spines (FD-7). It carries **five** properties at
any instant:

| Property | What it answers | Note |
|---|---|---|
| **Station** | where is this right now | dispositions change the station; nothing is ever deleted |
| **Age** | is this rotting | elapsed since the station was entered |
| **Clock** | when does this die | *time remaining* against the governing deadline (A-2) |
| **Dollars** | how much is inside it | may be zero; may not be absent |
| **Owner** | who must act | always a Roster entry (N-1) |

The clock is derived, never typed: a date the Thread already holds plus a Promise
(date of service plus the payer's filing window; the authorization's expiry; the
statement cycle; the thirty days on a demand letter). Age and clock answer
different questions and sort work differently — the huddle page sorts by clock,
the money page sorts by dollars.

**A Thread is minted at intent, not at arrival** (A-3). A booking mints one. So
does an inbound referral, a phone call that creates an obligation, a waitlist
entry, and a page landing in the Pile. Cancellation, reschedule, and no-show are
**dispositions** — a station change with an owner and a date — never deletions.
This is what allows readiness to sort appointments that have not happened and
allows a close sheet to dispose of a no-show; it also makes reschedule a station
change rather than a cancel-plus-create that silently orphans four artifacts.

Non-visit Threads are first-class and enumerated. The beta list is exactly:
**credit balance**; **unmatched referral**; **payer records request**;
**recoupment case**; **old-system balance** (the legacy AR running off in the
prior system, FD-9); **inbound item, unmatched**; and **inbound item, not ours**
(the misdirected-PHI case, which has a required notify-and-destroy disposition
and is not "trash") — the last two added by C-2, which also fixes dollars at zero
while keeping station, age, and owner mandatory from the moment of arrival.

*Naming adjudication.* FD-7 calls this contract "the Visit Thread". The visit is
its dominant instance, not its definition. The contract is written as **The
Thread**; "visit thread" survives only as the informal name of the visit-shaped
instance. Nothing else changes about FD-7: the contract is versioned and written
before any consumer.

**3. Readiness.** One computation, stated once: **what this needs, minus what we
know is true right now.** It is never a screen and never an API a user meets. It
is rendered as the sort order of the day, the patient's task list on their phone,
the claim's hold reason, the booking warning, the referral's blocking question,
the waitlist offer's eligibility test, and the desk's wave-through signal. It is
derived by dry-running the downstream operation — build the claim in memory; if
it validates, it is claim-ready — so it can never drift from what actually fails.
Three severities only: blocks booking (essentially never), blocks billing,
informational. **Hold the claim, never the patient**; a booked-anyway record is
not an error state, it is the evidence artifact.

**Close is Readiness applied to The Day.** What a closed day requires, minus what
is true right now. The close sheet is that list rendered to paper and signed;
the verb is not a seventh concept and the close checklist is never authored, only
rendered.

**4. The Promise.** A dated assertion with a **maker** (N-1 — on a Thread the
*owner* is who must act; on a Promise the party is who *asserted* it, and it is
usually not a Roster entry). A Promise admits two provenances (A-1):

- **Declared** — a maker and a document: a payer contract, an authorization with its units and expiry, a consent, a participation effective date, a Good Faith Estimate the practice issued, a price that holds until June 30.
- **Observed** — no maker, a sample size, a spread, and a window: *Aetna's median allowed for 99214 is $103.40, n=23, IQR $99.10–$107.80.*

Both are Promises; only the evidence differs. Per FD-2 the **observed promise is
the operational expectation** and the declared contract is the **arbiter**, loaded
only when the practice wants to dispute — and *disagreement between the two is the
single highest-value alert in the product*, because it means either the contract
was loaded wrong or the payer is not honoring it. Effective dating disappears as a
user-facing concept: the user only ever schedules a change ("starting on ___") or
looks at a day ("as of ___").

**5. The Money Line.** Money in three states (C-3):

- **Expected** — known but not yet landed: a card batch closed at 5:20pm that funds T+2, a funded ERA whose wire has not arrived, tomorrow's deposit. Not externally verifiable, and not an error.
- **Landed** — externally verifiable: a line on the bank statement, a check in hand, a counted drawer, a settlement report.
- **Explained** — landed and attributable to exactly one settlement, fee, transfer, or named non-patient item.

Only **landed-and-unexplained is red**. The money-day predictor is the sum of
expected Money Lines, which makes it arithmetic rather than a forecast — and that
is the difference between a number the owner trusts and one he does not. This is
the only home for PLB takebacks, merchant fees and the gross-versus-net seam,
chargebacks and their fees, returned items, payer interest, cash float and
over/short, and the deposit slip the product generates.

**6. The Roster.** Who works here — humans, robots, and the **external biller**
(FD-1) — in what job, at which locations, from when to when. Access, assignment,
coverage, absence, offboarding, and automation authority are pure functions of
it. There is no permissions screen; there is a staff list, and a robot is a roster
entry with a job title that can be fired the same way a person can.

**Capacity is a rendering of the Roster** (C-1): scheduling capacity is Roster
entries (person × location × working hours) projected onto a Day, and an open slot
is Roster time with no Thread on it. Say this explicitly, or the first person to
build the booking surface will invent a Schedule noun. **Counterparties are not
Roster entries** — the interpreter vendor, the referring office, the bank, the
payer's enrollment department appear as Promise makers, Money Line sources, and
Thread attributes. The outsourced biller *is* on the Roster, because she does the
practice's work under its authority.

**And one verb: Close.** Closing a day, a week, a month, or a quarter is the act
that produces evidence. Nothing in the system is ever *prepared* for an audit, an
insurance renewal, or a bookkeeper; the artifacts are the exhaust of a close that
had to happen anyway.

## The six surfaces

Everything in the catalog renders into six screens. Nothing else gets top-level
navigation.

| Surface | What it is | Who opens it, when |
|---|---|---|
| **Today** | One printable page: today's Threads sorted by readiness with the named blocker and its one-click fix, what is stuck by dollars × clock, today's five numbers, yesterday's close status. Opens the day and closes it. | Everyone, constantly |
| **The Patient** | One page indexing a party across Threads, Promises, and Money Lines: who's involved, what's true, what's owed, what's been said, what's next. The household is the unit wherever a family books, signs, pays, or is reminded. | Whoever is on the phone or at the desk |
| **The Thread** | One vertical timeline: one station, one age, one clock, one dollar figure, one owner, one next action — readable as a story. Also the patient's own view, the appeal packet's source, and the undo surface. | The biller; whoever answers "why hasn't this paid" |
| **The Money** | Did it come in; did they pay what they usually pay; where is it stuck; does it tie. Bookkeeper's version: one button and one reconciliation certificate. | Owner, ten minutes weekly; bookkeeper, monthly |
| **The Practice** | The Roster; what is expiring; what changes when; the binder. Readiness in negative form — everything that will not be true in fourteen days, with a name on each line. | Office manager, weekly |
| **The Pile** | One inbound inbox for everything that arrives — fax, upload, scan, payer mail, inbound text, a logged call — with one clock, one name per page, and one disposition. | Whoever is at the desk, every morning |

**Today and The Money are separated by a time horizon, not by subject.** Today
holds what is actionable today; The Money holds the same Threads rolled up when
they are not. Stating this prevents the two surfaces being built twice.

**The Pile is a station, not a container.** Every page that arrives becomes a
Thread on arrival and leaves the Pile the moment it has a disposition.

## The acceptance test

Every proposed feature must answer three questions:

1. **Which of the six nouns does it touch — or which party fact does it establish?** (amended by A-4; the second clause is what rescues merge aftermath, households, the minor-to-adult transition, and accommodation scheduling from failing the model's own test)
2. **Which of the six surfaces does it appear on?**
3. **What does it delete?**

A feature that introduces a seventh noun or a seventh surface needs an argument,
not a specification.

---

# Part 2 — The feature contract

Eight rules. Each is stated as something a reviewer can fail a document on. A
capability may not be staged until its document passes all eight, and
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)
records which slice exercises each rule.

## FR-1 — The frame rule (FD-1)

**Rule.** Every feature is designed for a practice with **two to four in-house
staff plus, commonly, one external biller**, and the frame governs surfaces,
defaults, and vocabulary — **never schema**.

**Test.** Name the person who performs this feature on a Tuesday and the hour they
do it. If the answer needs a role that does not exist at four staff — a router, an
approver who is not also the maker, a metric owner, a reviewer of reviewers, a
team to share with — the feature is mis-framed. Separately: if growing to fifteen
staff would require redesigning an object rather than adding machinery, the ceiling
has leaked into the schema and the design is wrong in the other direction.

**Why.** Every questionable verdict in the review traced to one source — a surface
asking the practice to author a policy it has never articulated. A fixed frame
deletes that entire class at once.

## FR-2 — The naming rule

**Rule.** No architecture vocabulary reaches a user-facing name, label, button,
column, notification, or printed page.

**Test.** Two forms, both shippable as definition-of-done lines: *the newest person
at the desk can explain this screen to a patient in one sentence without using a
product noun*, and *the screen's name would appear in a sentence the office manager
says out loud*. If neither holds, rename it before it ships.

**Why.** Document structure becomes navigation structure becomes the user's mental
model; fifteen atomic documents will otherwise become fifteen navigation entries
and the vendor's org chart becomes the product.

Retired vocabulary, with what replaces it on a user-facing surface:

| Never on a surface | What the page says instead |
|---|---|
| unapplied cash | money in, we don't know whose |
| PLB takeback / WO | Medicare's wire was $212.40 less than the claims on it |
| period close | July is printed |
| readiness evaluation / readiness contract | needs a person |
| previsit completion percentage | what's missing |
| effective-dated affiliation | Rosa is not approved with Ambetter yet |
| escalation ladder, acceptance window, SLA | 16 days to refile this or we lose it |
| accounting of disclosures, dead letters, replay eligibility | *(not user-facing at this size at all)* |

Also retired as *headline* phrasings, per FD-6: "queryable provenance API" and
"communication decision record". They remain substrate; they stop being claims.

## FR-3 — The valve rule

**Rule.** Every computed verdict, score, readiness state, detection, or metric
**names its consumer in the same document that produces it** — what it holds, what
it sorts, or what it releases.

**Test.** Point at the object whose behavior changes when the value changes. If
the only consumer is a human reading a screen, the feature is a report and must be
labelled one. Every gate must additionally name its **escape** (see FR-6).

**Why.** Twelve computed assertions in the packet had no consumer at all — most
consequentially, enrollment readiness with no claim hold behind it. A verdict with
no valve leaks; a gate with no escape blocks; both are the same omission, which is
modelling the decision and not what happens next.

Named valves the beta must carry: enrollment readiness → **claim hold with
retro-release**; previsit readiness → **wave-through at the desk**; appointment
readiness → **gate on the vacancy-recovery offer**; coverage change → **invalidation
of the estimates, statements, and claim-ready charges built on the old belief**;
authorization approved → **unit decrement and booking reservation**; open variance
or open appeal → **hold on the patient statement**; BAA on file → **connector
activation**; consent revoked → **autopay, cadences, and disclosure decisions,
retroactively**; observed promise → **the desk conversation and the estimate**;
detector emitting events → **detector-health canary**; metric value → **the queue
it should agree with**; override with a reason → **its eventual adjudication
outcome, so the rule can score itself**.

## FR-4 — The artifact rule (FD-6)

**Rule.** Every provenance link **names the artifact it emits and the minutes it
saves**. If no artifact can be named, that link stores less.

**Test.** Name the artifact and the person who touches it. The sanctioned list:
the one-click **appeal packet**; the auto-derived **secondary claim**; the
**retro-release list** with a dollar total; the **stuck-money worklist**; the
**preventable-denial count** with dollars; the **backwards leakage sweep**; the
**deposit slip**; the **signed close sheet** (which accumulates into the audit
binder a day at a time); the **signed migration reconciliation**; and **undo**.

**Why.** A two-to-four person practice never calls a trace API. Artifacts are the
same data made valuable — and undo is itself a provenance artifact, which makes
FD-5 and FD-6 one architecture: a chain built for undo must be complete in a way a
chain built for audit need not be.

## FR-5 — The rhythm rule

**Rule.** Every feat states its place in the day / week / month / quarter / year
rhythm, and every feat that produces work states its **clearing condition**.

**Test.** Name the ritual the feature appears in. A capability that belongs to no
ritual either attaches to one or is cut. A work type whose clearing condition is
"a human marks it done" must justify why the underlying state cannot clear it.

**Why.** A practice does not experience a queue; it experiences Tuesday. The day
boundary is where money and data quality are actually lost, and four reviewers
independently named a day ritual as their cluster's largest omission.

The rhythm the product ships pre-populated, so the practice inherits an operating
rhythm instead of being asked to invent one:

| Cadence | What happens |
|---|---|
| **Day** | Morning huddle page opens it; End-of-Day Close shuts it and emits the signed day record; an unclosed day appears on tomorrow's huddle |
| **Week** | Next week's authorizations and unconfirmed appointments; the payer drift digest; the owner's ten-minute money page |
| **Month** | The bookkeeper's package and reconciliation certificate; the owner's one-pager; the statement cycle |
| **Quarter** | The evidence binder; access review; the restore drill |
| **Year** | Risk analysis; contract renewal conversations; code-set update; credential revalidation |

**Print is a first-class medium.** The huddle page, the close sheet, the money
page, the deposit slip, and the binder are designed to one sheet first. Print-first
is a constraint that materially improves the digital version.

## FR-6 — The governance rule (FD-5)

**Rule.** Approval is replaced by **reversibility, visibility, and delay**, shipped
as four fixed tiers. **Tiers are not authorable.** Every gate carries a deadline
escape.

| Tier | Behavior | Applies to |
|---|---|---|
| **1 — Do + undo** | Reversible, silent, undoable from the record | the overwhelming majority of work |
| **2 — Do + notify** | Reversible, surfaced in Today and in the close | posting, adjustments within threshold, contact changes |
| **3 — Delay + cancel window** | Executes after a visible countdown unless cancelled | claim batches, statements, outreach, prior-auth requests |
| **4 — Hard stop** | True approval by a human | money leaving the practice, material adjustments, record merges |

**Test.** Every action that moves money, sends something outside the building, or
changes a record's identity is assigned exactly one tier in its feature document,
and every tier-3 and tier-4 gate names the deadline that releases it. A gate that
would stall a claim past timely filing **auto-releases at tier 3 with notification**,
because the gate otherwise destroys exactly what it protects.

**Why.** The second human does not exist — five reviewers independently reached
this replacement — and approval queues rot within a month. Tier 4 preserves the
constitution's line against autonomous money movement.

*Flagged, not reconciled:* the repository constitution (Principle II) states that
autonomous financial or patient-impacting actions "require explicit human approval
flows." Tier 3 sends statements and claim batches after a visible cancel window
with no affirmative click. That is a defensible reading of "explicit human
approval" — the human is shown the action and given the power to stop it — but it
is a reading, and a brainstorm document cannot amend a constitution. Before any
tier-3 outbound action is staged, either the constitution is amended or the reading
is recorded in the proposal's Constitution Check. See the open risk in
scope-and-sequencing.

## FR-7 — The licensing rule (FD-11)

**Rule.** **License-free core, licensed overlay, never licensed content in the
repository.**

- The open-source core ships only license-free content: ICD-10, HCPCS Level II, place-of-service, CARC/RARC, and the *structure* for procedural coding.
- CPT® ships as a **bring-your-own-license overlay pack**, imported at first run from the practice's own licensed source.
- X12 knowledge is **abstracted behind the clearinghouse connector**, so the product need not redistribute implementation guides. A future direct-to-payer path ships as a hosted service that carries the license.
- A future hosted offering may bundle licensing commercially; the repository never does.

**Test.** For every code set, fee table, crosswalk, or implementation guide a
feature reads, name its license status and its distribution path. A feature that
cannot name both must not carry the content in the repository.

**Why.** This is the only shape that preserves open-source distribution without
either shipping unlicensed IP or making the product unusable out of the box. It
also forks onboarding cleanly: first-run setup includes "load your code sets",
which progressive activation already accommodates.

*Open risk.* AMA licensing terms for practice-held CPT rights require legal
verification before the onboarding flow is finalized. Tracked as an explicit open
risk with a named action in scope-and-sequencing.

## FR-8 — The learning rule

**Rule.** The practice's own artifacts and history are the **primary configuration
source**; hand authoring is the fallback and must be justified.

**Test.** For every setup field a feature introduces, name the artifact it could be
read from — a prior CMS-1500 or 837, one remittance, the W-9, an NPPES lookup, a
CAQH export, last year's charge lines, twelve months of actual bookings — or record
why no such artifact exists.

**Why.** Setup abandonment is independently named as the most likely cause of beta
failure, and configuration surfaces are independently named as the largest source
of enterprise smell. This principle appeared as an *alternative solution* twenty
times across six reviews and was never stated as a rule; stating it retires a dozen
screens at once. A prior claim is a complete, authoritative statement of the
practice's own billing structure, and it is the cheapest master-data import in
healthcare.

## The eleven-point document checklist

The rules above govern shape. The checklist below governs completeness, and is
retained unchanged in substance from the prior revision — reviewers specifically
endorsed items 4 and 8 as the two most likely to save the project, and both are
habitually skipped without a checklist. Item 5 is amended to the four tiers.

Before a candidate moves from brainstorm to staging, its document should identify:

1. the primary actor and observable outcome;
2. trigger, preconditions, and terminating conditions;
3. authoritative owner and explicitly excluded ownership;
4. state machine, including cancellation, correction, expiry, and reopening;
5. **the governance tier of every action, its deadline escape, and its undo path** (replacing "approval thresholds and separation of duties", which does not exist at this size);
6. data, evidence, provenance, retention, and disclosure behavior;
7. integration version, idempotency, correlation, and replay semantics;
8. exception, outage, offline, and assisted fallback paths;
9. security, accessibility, language, and equity scenarios;
10. success, safety, and operational metrics with formula versions;
11. smallest useful release slice, dependencies, non-goals, and acceptance scenarios.

## Ownership seams

A work list owns the lifecycle of work, not the claim, referral, credential, or
payment that caused it. An integration connector owns transport and partner
protocol behavior, not the domain meaning of an authorization or remittance.
Reporting owns metric semantics, not mutable copies of source records. The EHR
owns the clinical fact; openPractice owns the **administrative consequence** of it,
which is why an unsigned note appears here as a Thread at the waiting-on-clinical
station with a name and a date rather than vanishing from both systems. These seams
should become explicit contract tests.

---

# Part 3 — The catalog

## Capability classes

| Class | Purpose | Examples |
|---|---|---|
| Business domain | Owns authoritative administrative or financial state and its lifecycle | registration, scheduling, coverage, charges, claims, patient receivables, **phone and the inbound pile** |
| Shared domain service | Applies one governed behavior across several business domains | communications, the work list, metric semantics, the Readiness evaluator |
| Platform control | Enforces contracts, security, audit, recovery, and data lifecycle | interoperability, authorization, audit, retention, connector operations |
| Experience or channel | Presents tasks from owning domains without becoming authoritative | responsive patient flow, kiosk, the patient money view, staff workspace |
| Optional intelligence or sensing | Improves an existing deterministic workflow under additional evaluation and consent | intake assistance, prediction, geofence or Bluetooth arrival evidence |

## Normalized reading of the fifteen domains

| Existing brainstorm | Bounded beta interpretation | Class |
|---|---|---|
| [Registration And Demographics](openpractice-feature-list-registration-and-demographics.md) | Maintain the party and place register — identity, household, guarantor, guardian, subscriber — with merge *and its aftermath*, and party facts as first-class (A-4). | Business domain |
| [Scheduling And Patient Access](openpractice-feature-list-scheduling-and-access.md) | Mint Threads at intent and place them against capacity rendered from the Roster (C-1); reschedule is a station change, not a cancel-plus-create. | Business domain |
| [Patient Engagement And Communications](openpractice-feature-list-patient-engagement-and-communications.md) | Decide, send, receive, and route minimum-necessary administrative messages under purpose-, recipient-, and channel-specific authority, at governance tier 3 for outbound campaigns. | Shared domain service |
| [HealthLinc App Onboarding](openpractice-feature-list-healthlinc-app-onboarding.md) | Deferred post-beta as a channel. Its identity-binding and household principles are extracted into registration; the trademark question on the name remains unresolved. | Experience or channel (deferred) |
| [Mobile Registration And Previsit Completion](openpractice-feature-list-mobile-registration-and-previsit.md) | One visit task list with a URL that needs no account, rendered anywhere and closable by staff on the patient's behalf. Kiosk and app are renderings of it. | Cross-system experience |
| [AI Intake Assistance](openpractice-feature-list-ai-intake-assistance.md) | Deferred behind its prerequisite data. Its deterministic twins move to their owning documents. | Optional intelligence (deferred) |
| [Arrival Presence Detection](openpractice-feature-list-arrival-presence-detection.md) | Reduced to arrival provenance, an inclusive ladder, and the one-click delay broadcast; geofence and Bluetooth deferred. | Business workflow plus optional sensing (mostly deferred) |
| [Coverage And Authorizations](openpractice-feature-list-coverage-and-authorizations.md) | Maintain declared Promises — coverage, authorization units and expiry, participation — and feed Readiness. Never guarantees payment. | Business domain |
| [Charge Capture And Coding](openpractice-feature-list-charge-capture-and-coding.md) | Derive reviewable charges from administrative events and immutable EHR references, under a **license-free core catalog plus a licensed overlay** (FR-7) resolved by date of service. | Business domain |
| [Claims And Remittance](openpractice-feature-list-claims-and-remittance.md) | A connector-neutral transaction state machine: edits, submission, acknowledgments, status, attachments, remittance, corrections, voids, secondaries, denials, appeals — each step a station on the Thread. | Business domain |
| [Patient Billing And Payments](openpractice-feature-list-patient-billing-and-payments.md) | One receivable lineage across estimate, GFE, time-of-service collection, statement, the no-account pay link, allocation, plan, dispute, credit, refund, and export. | Business domain |
| [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md) | One list of stuck Threads sorted by dollars × clock, with a fixed catalog of work types each carrying a clearing condition. **Routing, SLAs, delegation, escalation ladders, and team sharing are deleted.** | Shared domain service |
| [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md) | Five daily numbers and six monthly ones with frozen formulas, published fixtures, and tap-to-rows. **Period-close behavior is deleted** in favor of as-of queries and export-batch freeze (FD-3). | Shared domain service |
| [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md) | Access as a function of the Roster; audit integrity, privacy operations, recovery, retention, and evidence emitted by Close. **The permission matrix editor, review campaigns, disclosure accounting, and the public API program are deferred.** | Platform control |
| [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md) | Connectors as managed assets with credential lifecycle, idempotency, correlation onto the Thread, and a **manual connector** so fax, paper, and portal work keep the same status surface. | Platform control |

## Capabilities the fifteen domains do not own

These are the beta-eligible capabilities that had no owning atomic. They are named
here so that scope-and-sequencing can sequence them and so that no slice may
reference a capability this document has not defined. Priority: **B** =
beta-blocking, **V** = beta-valuable, **P** = post-beta.

### The Day and the channels

| Capability | What it owns | Pri |
|---|---|---|
| **Morning Huddle Page** | One printable page that opens the day: Threads by readiness, who is in and out, what is left over, what is running out of clock. | B |
| **End-of-Day Close** | Readiness applied to the Day, rendered and signed; emits the day record; an unclosed day appears on tomorrow's huddle. | B |
| **Phone Console And Call Disposition** | Caller-ID screen-pop, one-click book/message/callback, a required disposition, and a five-second capture from any screen. | B |
| **One Inbound Pile** | One searchable, aging, assignable inbox for fax, upload, scan, and payer mail; every page is a Thread on arrival with one disposition. | B |
| **Confirmed Fax Destination Registry** | Verified numbers, contacts, and formats per referring office; doubles as the referral-source master record. | V |
| **Misdirected PHI Disposition** | Notify-and-destroy handling for inbound items that are not ours, with the evidence trail "trash" cannot provide. | V |
| **Snooze With A Reason** | First-class deferral that preserves original age and returns on the date. | B |
| **Walk-In And Same-Day Access** | Entry with no prior appointment: find-or-create, decide capacity, compressed readiness at the desk. | B |
| **Desk-Side Real-Time Exception** | A fifteen-second staff-side resolution for every readiness blocker while the patient is standing there. | B |
| **Assisted Mode** | One universal keystroke recording actor, authority, and reason for any patient-facing task done on the patient's behalf. | B |
| **Recurring Practice Calendar** | The operating rhythm shipped pre-populated (FR-5). | V |
| **Coverage Day And While-You-Were-Out** | Mark yourself out; work pools; a return briefing. Replaces the delegation subsystem. | V |
| **Provider Absence And Mass Schedule Disruption** | The doctor is out: reschedule twenty-two Threads, notify, offer alternatives, track who landed somewhere. | V |

### Party facts and the front office

| Capability | What it owns | Pri |
|---|---|---|
| **Patient Merge Aftermath And Reversal** | What follows a merge across appointments, claims, balances, forms, links, and threads, plus a 30-day reversal. | B |
| **Reschedule As Carry-Forward** | Carry the previsit plan, estimate, eligibility, cadence, and issued link; invalidate only what genuinely changed. | B |
| **Household And Family Coordination** | Booking, checking in, signing, and paying for a family block against one guarantor. | V |
| **Minor-To-Adult Authority Transition** | Guardian roles expiring at majority, re-consent, communication re-routing. | V |
| **Interpreter And Accommodation Scheduling** | Language, ASL, mobility, service animal, extra time as booking constraints that log the arrangement as evidence. | V |
| **Urgent-Content Safety Property** | Deterministic keyword escalation and an unconditional after-hours reply naming the emergency path, the moment two-way SMS exists. | B |
| **Referral-In As An Entry Point** | The inbound referral as a Thread from arrival through match, booking, and closed-loop reply to the sender. | V |

### Money

| Capability | What it owns | Pri |
|---|---|---|
| **Self-Pay And Time-Of-Service Money Path** | A priced, discountable, receipt-bearing, GL-destined path for patients who never generate a claim, plus collection at check-in (FD-4). | B |
| **Good Faith Estimate Register And Dispute Clock** | Issue, version, deliver, and retain the written GFE; watch billed-versus-estimated variance; assemble the dispute file at threshold. | B |
| **Price-By-Date-Of-Service** | Resolve self-pay prices, offers, and GFE amounts by service date so a mid-cycle change never reprices a prior estimate. | B |
| **Patient Money View** | Balance, why, and pay-now behind a texted link with no account required (FD-10). | B |
| **Bank Line Inbox** | The bank statement as a to-zero worklist; every credit and debit explained by exactly one thing. | B |
| **Merchant Settlement, Fee, And Chargeback Reconciliation** | Gross versus net funding, per-batch and monthly fees, reserve holds, chargebacks, returned items, each with a destination account. | B |
| **Payer-Level Adjustment (PLB) Ledger** | Takebacks, forwarding balances, interest, capitation, early-payment allowances resolved to a claim or a named non-patient account. | B |
| **Manual EOB And Paper-Check Intake** | A structured path for paper remittances producing the same posting, variance, and provenance objects as an 835. | B |
| **Recoupment And Overpayment Case File** | Demand letter → appeal-or-agree → refund or offset election → the future remittance line where the offset lands. | B |
| **Credit-Balance Clocks By Cause** | Unidentified receipts, patient credits, payer overpayments — each with a cause classifier and days *remaining*. | B |
| **Refund Disbursement Register** | Refunds as a liability from approval through issuance to clearance, with the 60-day clock and an escheatment endpoint. | B |
| **Daily Balanced Day** | Cash float and over/short, card batch, posted receipts, deposit slip — the five-minute control point inside the Close. | B |
| **Money-Day Predictor** | Tomorrow's deposit as the sum of expected Money Lines. | V |
| **Immutable Money Ledger And Reason Taxonomy** | Every dollar as one immutable row with one reason and one destination account; the taxonomy is the GL join key and is defined before anything posts. | B |
| **Accounting Export, QuickBooks-Shaped** | One journal entry per bank deposit, cash-basis default, at most eight mapped accounts, with a one-page reconciliation certificate. | B |
| **Export-Batch Freeze** | Once exported, a journal batch is versioned and immutable; later corrections land in the next batch with back-references (FD-3). | B |
| **Non-Visit Revenue Capture** | Time- and period-based services with no appointment to derive from. | V |
| **Workers' Comp, Auto, And Liability Billing** | The distinct payer model: claim number, adjuster, date of injury, jurisdictional schedule, no eligibility transaction. | V |
| **Patient Financing Settlement** | Third-party financing as a full-balance settlement net of a discount. | P |

### Promises, claims, and expectation

| Capability | What it owns | Pri |
|---|---|---|
| **Enrollment-Gated Claim Hold And Retro-Release** | Hold — not reject, not submit — claims for any provider/payer/location pair not yet effective; release every eligible held claim in one action when the effective date lands. | B |
| **Substitute And Supervising Billing Arrangements** | Locum (Q6 with its day countdown), reciprocal billing (Q5), incident-to, split/shared — so readiness answers "billable under whom, with what modifier, until when". | B |
| **Learned Payer Baseline** | The observed Promise: median allowed by payer × code × modifier × place of service, with n and spread (FD-2). | B |
| **Payer Drift Alarm** | Weekly comparison of each payer's recent allowed amounts to its own trailing baseline; output is two or three payer behaviors, never a queue of lines. | V |
| **Secondary And Tertiary Claim Derivation** | Build the next payer's claim from the posted primary remit, including prior-payer adjudication amounts, plus crossover detection. | B |
| **Corrected Claims, Voids, And Replacement Control** | Replacement and void with the original payer control number, a diff of what changed, duplicate-denial protection. | B |
| **Timely-Filing And Deadline Control Plane** | One clock per claim and per appeal, seeded from payer policy, with the deadline-aware escape from every gate (FR-6). | B |
| **Payer Records-Request Tracker** | "A payer asked us for documentation" as a channel-agnostic Thread with deadline, claim, what was sent, and the acknowledgment. | B |
| **Waiting-On-Clinical State** | The visible, measured, unowned station for administrative work blocked on an EHR action. | B |
| **Appeal Packet** | Eligibility + authorization + charge + claim as submitted + acknowledgment + remittance + letter, assembled in one click (FR-4). | B |
| **Provider Onboarding And Departure Runway** | Backwards-planned countdown from hire date to first-billable date, and its mirror on departure. | B |
| **Payer Follow-Up By Batch** | Group stalled claims by payer so one twenty-minute call resolves twelve Threads, with bulk disposition and one shared evidence note. | V |
| **Contract-As-Arbiter Import** | Optional, per-payer declared Promise loaded only to dispute; disagreement with the observed Promise is the alert. | V |

### The Roster and the platform

| Capability | What it owns | Pri |
|---|---|---|
| **The Roster** | People, robots, and the external biller; access, coverage, absence, and offboarding as functions of employment facts. | B |
| **Same-Day Offboarding Switch** | One action that kills sessions, revokes tokens, forces rotation, reassigns work, and files the dated record. | B |
| **Credential And Certificate Expiry Radar** | One inventory of every secret, key, certificate, license, and portal login with an owner, an expiry, and a what-breaks note. | B |
| **BAA-Gated Vendor Activation** | No connector, clearinghouse, processor, fax service, or backup destination activates without a linked, dated, unexpired BAA. | B |
| **Test/Production Firewall** | Structural bidirectional isolation between synthetic and real patients, enforced at the spine and visible as a banner. | B |
| **Silent Failure Detector** | Alert on the *absence* of expected activity, including a detector that has emitted nothing in a week. | B |
| **Support Access With Consent** | A time-boxed, consent-gated, logged path for an engineer, with a PHI-scrubbed diagnostic bundle. | B |
| **Upgrade Safety Rail** | Pre-upgrade snapshot, post-upgrade smoke suite, automatic rollback, configuration-drift report. | B |
| **Right Of Access Clock** | Patient record requests with identity verification, a visible 30-day countdown, and a completion receipt. | B |
| **Practice Change Log With Undo** | One reverse-chronological human-readable feed of every configuration change, with revert. | B |
| **Scheduled Changes Calendar** | Every future-dated change — price, provider start, enrollment effective date, form version — with owner and one-click cancel. | B |
| **The Daily Five And Monthly Six** | Frozen metric formulas with published fixtures, visible denominators, normal bands, and tap-through to the rows behind every number. | B |
| **Data Trust Panel** | Everything that could currently make the numbers wrong, shown next to the numbers. | V |
| **Practice Health One-Pager** | The monthly owner PDF: cash in, collections versus expected, aging, visits versus last year, three problems with dollars and one action each. | V |
| **Compliance As A Dated To-Do List** | One inbox of periodic obligations where completing the task is what emits the evidence. | V |
| **Recovery Day** | A quarterly 45-minute restore exercise producing a signed certificate with a wall-clock RTO. | V |

### Implementation

| Capability | What it owns | Pri |
|---|---|---|
| **Progressive Activation And The Six Firsts** | Schedule → eligibility → charge → claim → remit → banked day, each activated when its "first" succeeds (FD-9). | B |
| **Open-Items Migration And Balance Reconciliation** | Future appointments, active patients, open balances as summary threads; a signed one-page reconciliation; rejects as work items. | B |
| **Old-System Balance Thread** | Legacy AR running off in the legacy system, visible as watched work so the owner's money view shows whole-practice truth. | B |
| **Remittance History Import (Evidence Only)** | Read-only prior EOB/ERA history imported to seed the observed Promise, behind an explicit pre-openPractice provenance boundary; never posts to the ledger. | V |
| **Code-Set Core And Licensed Overlay Import** | First-run load of license-free core sets plus the practice's own CPT overlay (FR-7). | B |
| **The Thread Contract** | The versioned correlation identity: what it identifies, when one is minted, how children inherit, re-association, the unknown state, merge, split, and the migration boundary (FD-7). | B |

## Reclassifications made by this revision

- **Phone and fax are owned capabilities, not channel footnotes.** The beta overview's system diagram promises `staff · telephone · responsive web · kiosk · future app · fax/API` and no atomic owned telephone or fax. The Phone Console and the One Inbound Pile are now Tier-1 business-domain capabilities (FD-10a). Phone is the dominant small-practice channel; leaving it invisible made every queue and every accountability metric structurally wrong from week one.
- **The companion app stays deferred; the patient money view does not.** Balance, why, and pay-now behind a texted link with no account is beta scope (FD-10b) — it is the payments half of the FD-4 money path and the place patient dollars actually come from. The app's deferral is reinforced by the unresolved trademark question on the HealthLinc name.
- **AI, prediction, and sensing remain deferred behind their prerequisite data.** Denial prediction cannot be meaningful before denial-disposition history exists; no-show prediction cannot be meaningful before a year of dispositions exists. Deferral is not rejection: each returns when its prerequisite deterministic workflow, consent model, evidence, and evaluation data exist. The deterministic twins inside those documents move now to their owning capabilities.
- **The approval framework is deleted** and replaced by FR-6's four tiers; **routing, SLAs, delegation, escalation, and team sharing are deleted** and replaced by one list sorted by dollars × clock; **the period-close ceremony is deleted** and replaced by immutability at post time, as-of queries, and export-batch freeze.
- **Contract modelling demotes to an arbiter import.** Fee schedules, case rates, carve-outs, and precedence hierarchies leave the beta's critical path; the observed Promise takes their place (FD-2).

## Current corrections and refreshes

- Replace language such as "fully automated administration" with straight-through processing only where authority is pre-recorded, the action is reversible, and exceptions are visible. Consequential actions are governed by the four tiers of FR-6, not by prose.
- The [front-office synthesis](openpractice-feature-list-synthesis-front-office-and-access.md) links seven atomic members but calls them a "sextet"; the next explicit update should correct that editorial mismatch.
- Competitive assertions should carry URLs, capture dates, product/version context, and a scoring method. The phrase "statistically tied expanded set" should either link its method or be rewritten without a statistical claim.
- The patient-app topology question is **closed for the beta** by FD-10: no app, therefore no federation and no third authoritative store. The question returns intact if and when the app is reconsidered post-beta.
- Claims attachments should move from an open question into the claims and interoperability scope. CMS finalized X12 275/277 and adopted HL7 attachment standards in 2026, with compliance scheduled 24 months after the May 26, 2026 effective date in the [CMS claims-attachments fact sheet](https://www.cms.gov/newsroom/fact-sheets/administrative-simplification-adoption-standards-health-care-claims-attachments-transactions). For the beta this lands as the **Payer Records-Request Tracker** — usable today over fax and portal, and the natural home for the X12 275 when it arrives.
- Prior authorization should explicitly model requirement discovery, documentation, request, response, denial reason, and clock behavior around the CMS 2026 operational provisions and generally 2027 API dates in the [CMS prior-authorization final-rule summary](https://www.cms.gov/newsroom/fact-sheets/cms-interoperability-prior-authorization-final-rule-cms-0057-f), while retaining manual and X12 paths. The authorization is a declared Promise with units and an expiry; the clock (A-2) is what the huddle page displays.
- Patient estimates must distinguish the ordinary insured estimate from the written Good Faith Estimate and its dispute-evidence workflow, as described by [CMS for uninsured or self-pay patients](https://www.cms.gov/initiatives/your-patient-rights/medical-bill-rights/know-your-medical-bill-rights/know-your-medical-bill-rights-when-not-using-insurance). **This is no longer an editorial note.** Per FD-4 it is a beta-scoped money path — estimate → GFE where required → collect at visit → receipt → thread closed same day — and it is sequenced in Slice 2 of scope-and-sequencing. The insured estimate is schedule-of-charges arithmetic plus observed-Promise copay and deductible hints, not benefit-engine modelling; the GFE is a document with required elements, delivery timing, a retained copy, and a dispute clock.
- Security controls should be versioned against current law and separately track proposed rules. HHS currently distinguishes the Security Rule in effect from proposed modifications in its [Security Rule summary](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

## Interfaces and boundaries

This document defines the work model, the feature contract, the capability
register, and catalog shape. It does not ratify scope, create implementation
tasks, replace the existing competitive research, or select a technical data
model. FD-1's frame governs surfaces and defaults and explicitly **does not**
govern schema. Existing atomics remain linked evidence and design space; future
staged features should cite the relevant research rather than copy it.

The register above is a naming authority, not a commitment: a capability appearing
here is eligible to be sequenced, not promised. What is committed to the beta is
decided in
[Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md).

## Alternatives and tensions

Keeping the fifteen domains intact is simpler for navigation but obscures which
ideas are foundations versus channels. Decomposing every possible feat immediately
would create a large pseudo-backlog before the launch customer and connector set
are fully known. A universal rules engine could reduce repetition, yet
domain-specific policies with a shared evaluation envelope are safer than an
unconstrained cross-domain language. Strict templates improve completeness but
must not turn brainstorm capture into premature specification.

Two new tensions arrive with this revision. First, **the work model is deliberately
incomplete**: it says nothing about the things it operates on, which is a virtue
while declared and a trap if the declaration is dropped in a later edit. Second,
**eight rules are seven more than most teams enforce**; the mitigation is that each
one is a single question a reviewer asks, and six of the eight delete surface
rather than adding it.

## Open questions

Settled by the ratified framing decisions and no longer open: the staff-count
frame, the expectation source, the close, the estimate and GFE question, the
governance primitive, the differentiator statement, spine ownership, the first
external milestone, cutover, channels, and the licensed-IP distribution shape.

Still open:

- Should the durable catalog assign stable capability IDs before staging, or only when a capability enters the OpenSpec/Speckit lifecycle? The register above uses stable **names** as the interim contract.
- Which of the normalized domains may be satisfied for beta by an explicit manual workflow plus the manual connector, rather than by built capability?
- Should the visit task list be owned by openPractice, by a neutral cross-product experience service, or by coordinated task APIs from openPractice and openChart? The app's deferral postpones but does not answer this.
- What research-evidence format supports repeatable competitor comparisons without copying proprietary material?
- Which cross-domain policy envelopes should be shared, and which rules must remain domain-specific?
- **Append-only integrity versus required deletion.** Retention limits, minors' rules, patient-requested restrictions, and data minimization all require deletion from a structure defined as immutable, and no ratified decision covers it. It must be decided in the ledger's design before the chain has a year of history to invalidate. Deliberately left unresolved here and tracked as an open risk in scope-and-sequencing.
- **Which system is authoritative for the appointment** across the openPractice/EHR boundary — named by reviewers as the most contested object in every PM/EHR pairing and still implicit.

## Relationships

Combined with [Small Ambulatory Practice Beta Archetype](openpractice-small-practice-beta-launch-archetype.md)
and [Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md)
in [Synthesis: Small-Practice Product Shape](openpractice-small-practice-beta-synthesis-product-shape.md).
Revised against the ratified [Framing Decisions](../review/openpractice-framing-decisions.md)
and the [Paper Prototypes](../review/openpractice-paper-prototypes.md) verdict,
both derived from [Feat Review Synthesis](../review/openpractice-feat-review-synthesis.md).
The work model in Part 1 is the synthesis §3 model as amended by the prototypes;
the capability register in Part 3 is the synthesis §4 catalog with priorities
preserved. Supporting detail for the money capabilities is in
[Revenue Integrity And Product Shape](../review/openpractice-feat-review-revenue-integrity-and-product-shape.md)
and for the day rhythm in
[Operations And Intelligence](../review/openpractice-feat-review-operations-and-intelligence.md).
</content>
</invoke>
