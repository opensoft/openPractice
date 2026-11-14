# The Roster: People, Robots, Access, Coverage, And Offboarding — Brainstorm

Status: brainstorm
Kind: report
Summary: access at a four-person practice is a pure function of employment facts, so openPractice ships a staff list and no permissions screen — seven shipped role recipes that compose but never edit, one same-day offboarding switch whose seven effects and dated record are the evidence, absence that covers rather than reassigns and a return briefing that falls out of it, temps and locums and the support engineer modelled as one object (a roster entry that expires), every robot on the same list with a job title and a supervisor and the same switch, the external biller first-class and BAA-gated, and the day's capacity, huddle, coverage, and close signature all rendered from the same list rather than from a second system.
Topics: openpractice-small-practice-beta, roster, access, offboarding
Repository context: openPractice — the Roster capability for the first small ambulatory practice beta (Slice 0, beta-blocking)
Captured: 2026-08-27

## Possible feats

- **The roster is the only access surface** — one screen called *Who works here*; adding a person with a shipped role recipe is the complete access-administration act, and there is no permissions matrix anywhere in the product.
- **Role recipes shipped, not authored** — seven named, versioned, upgrade-maintained recipes with plain-English summary cards; they **compose but never edit**, so per-person tailoring cannot rebuild the matrix one row at a time.
- **The same-day offboarding switch** — one action with seven fixed effects and one dated termination-of-access record, run identically for a person, a robot, an external biller's contract, and a support grant.
- **Absence covers, departure reassigns** — marking yourself out withdraws capacity, pools your work under a named cover, and keeps your name attached; only a departure rewrites ownership, and the difference is what makes the return briefing computable.
- **Access with an end date built in** — the temp, the locum, and the vendor support engineer are the same object: a roster entry that expires on a date, so nobody has to remember to end it.
- **Robots on the staff list** — every automation is a roster entry with a shipped job title, a human supervisor, a start and end date, the same offboarding switch, and one all-stop; bounded automation with no scope designer.
- **The roster renders the day** — schedulable hours, who is in and out on the huddle page, who covers, and who signs the close are all projections of this one list (C-1), so no Schedule object and no second workforce system is ever invented.

## Focus

At two to four in-house staff plus an external biller, who is allowed to do what is not a policy anyone has articulated — it is a set of employment facts the practice already maintains for payroll. What is the smallest object that can carry those facts precisely enough that access, assignment, capacity, coverage, absence, expiry, automation authority, vendor support, and the quarterly access review are all consequences of it rather than nine features built beside it?

## Current state: a matrix editor for a practice with six jobs

The platform-and-trust review's central finding is that this cluster's architecture is largely correct and its surfaces are largely wrong. The grant model underneath — staff × service × location × object × field × action — is essential and cannot be retrofitted. The **matrix editor on top of it is an enterprise artifact aimed at a role that does not exist here**: a nine-person practice has roughly six distinct jobs, and the office manager will never open a six-dimensional designer, because when she opens that screen she has just hired a front-desk person and needs them working in four minutes.

That review's second reframing states the replacement in one line: **"payroll is the access control system."** Access is a pure function of employment facts — who works here, in what job, at which locations, from when to when. There is no permissions screen; there is a staff roster, and the roster is a document the practice already keeps for other reasons. The review's own accounting of what that single move collapses is the reason this document exists: it retires the RBAC matrix, the same-day offboarding gap, the access-review campaign, the temp-worker problem, and — if automations are modelled as employees — the automation scope model, all into one object.

The gaps the review found in the existing feats are not gaps in the model; they are the model's missing verbs, and they are the highest-frequency real failures at this size:

- **Same-day offboarding is entirely unaddressed.** Nothing revokes live sessions, kills tokens the person created, rotates shared credentials they knew, reassigns their open work, or files a dated termination record. This is named as *the single highest-frequency real HIPAA access failure at this practice size*.
- **No model for the outside billing service** — a contractor organization needing scoped access under a BAA, with its own offboarding when the contract ends. FD-1 ratified her as first-class; nothing modelled her.
- **No handling of the temp, locum, or per-diem** — access that should expire on a date without anyone remembering to end it.
- **No shared-credential story.** Break-glass is usually a symptom of the front desk that all logs in as "reception"; detecting and eliminating shared credentials is worth more than governing emergency access.
- **No support path.** Without a sanctioned one, real support happens over screen-share using the office manager's credentials, *which quietly voids the entire access-control position*.

The operations-and-intelligence review arrives at the same object from the opposite direction. It names the practice's four actual problems — a memory problem, a day-boundary problem, an "is this normal?" problem, and a **single-point-of-failure problem** ("the one person who knows is out Thursday") — and finds the proposed delegation machinery three sizes too large for the last one. Its prescriptions are the coverage half of this document: coverage is *a fact about a day*, not a workflow; one absence model with four consumers, because separate staff-out-of-office and provider-unavailability concepts would be a defect rather than two features; and the **return-from-absence briefing** as the underexplored half — *coverage handles the leaving; nothing handles the coming back*.

FD-1 fixed the frame and put the external biller on this list. C-1 made capacity a rendering of it. N-1 made every Thread owner an entry in it. The normalized work model states it in one paragraph and then stops, and every one of the six surfaces assumes it. This document is the paragraph expanded to a contract.

## Proposed feature set

### 1. One surface, and it is a staff list

**There is no permissions screen in openPractice.** There is a screen on **The Practice** called *Who works here*, and it is a list of names. Each row carries the six employment facts and nothing else:

| Fact | What it governs | Read from (FR-8) |
|---|---|---|
| **Name** | attribution on every transition, forever | the payroll register; the legacy system's user list |
| **Job** | the role recipe, therefore the whole grant set | the payroll register's job title, matched to a shipped recipe |
| **Locations** | which sites' work is visible, and where capacity lands | the practice's own location register |
| **Working hours** | the Day's capacity, and who is on duty when | **twelve months of the legacy system's actual bookings**, not authored |
| **Start date** | when access begins; nothing works before it | the payroll register |
| **End date** | when access ends, automatically | the payroll register; blank for permanent staff |

A seventh fact exists only for robots and external participants: **supervisor**, the human roster entry accountable for it.

Adding a row is the complete access-administration act. Changing the job changes the access. Setting the end date is the offboarding. **Nothing else in the product grants, revokes, scopes, or reviews access**, and no screen anywhere says *permission*, *role binding*, *scope*, *principal*, *provisioning*, or *least privilege* (FR-2).

Two properties make the claim structurally true rather than aspirational, both taken from the review:

- **Enforcement lives at the data layer, below every entry point.** Row and field authority is evaluated once, beneath the app, the Desk, the API, and every background job. Per-surface checks drift within two releases and would make the "one list means the same thing everywhere" claim false the first time a new surface ships.
- **Field-level protection is a default, not a policy.** SSN, bank account, and full card data are masked for every recipe out of the box; unmasking is a logged, reason-required, per-record action. Nobody configures this, so nobody can get it wrong.

### 2. Seven role recipes, shipped, composing but never editing

A recipe is a named, versioned, upgrade-maintained grant set with a plain-English card — *can see / cannot see / can take money up to $X* — that the office manager reads in fifteen seconds. The beta ships these and no blank one:

| Recipe | Said plainly | Notably cannot |
|---|---|---|
| **Front desk** | The schedule, the Pile, the patient's page, take money at the desk, work today's threads | adjust a balance, issue a refund, export a patient list, change the roster |
| **Clinician** | Their own day and panel, their threads' clinical waits, what a visit cost | anything in the money path beyond seeing it |
| **Practice manager** | Everything the practice does, plus this list, plus the binder | *(the only recipe that can change another person's job)* |
| **Biller** | Threads at charged, claimed, and remitted; posting, appeals, statements, adjustments within threshold | issue a refund, change the roster, export the patient list |
| **External biller** | The biller recipe, bounded: her own work queue, the threads she works, her own accountability | edit the party register, see the practice-wide money page, export anything, exist without a current BAA |
| **Bookkeeper** | Read-only money: the monthly package, the export, the reconciliation certificate | see a patient's chart, a schedule, or any clinical fact |
| **Support (time-boxed)** | Nothing by default; exactly what a consent grant names, for exactly as long as it names | exist without an unexpired grant; act unlogged |

**Recipes compose; they never edit.** A person holds one or more whole recipes and their access is the union. There is no subtraction, no per-field grant, and no "advanced" tab — because a per-person deviation *is* a permissions matrix, arriving one row at a time and undetectably. The physician-owner at the beta practice holds **clinician + practice manager**, which is how the model expresses a real arrangement without inventing an eighth recipe.

**The dangerous unions are named, and they produce an acknowledgment rather than a block.** Posting payments + adjusting balances + issuing refunds in one person is the classic small-practice embezzlement triangle, and at two to four staff it is frequently unavoidable. The review proposed a hard stop at the moment of grant. **FR-1 forbids that**: a gate requiring a second human who does not exist is mis-framed, and it will be defeated within a week. Instead the union is permitted and produces a dated, signed **compensating-control acknowledgment** — *"I accept this; the practice owner reviews the adjustment report monthly"* — which files itself into the binder. Segregation-of-duties impossibility is normal at this size; **unacknowledged** impossibility is the finding.

*Deferred, deliberately:* **least privilege by observation** — the day-60 card reading *"Sarah has been able to issue refunds for 60 days and never has. Remove?"* — is genuinely valuable and is the honest way access converges without a security architect. It is post-beta here for one reason: its "remove" button is a per-person subtraction, which is exactly the rule above. It returns when it can be expressed as *dropping a whole recipe*, not as trimming one.

### 3. The same-day offboarding switch

One action. The office manager types *Mike's last day is today* and the switch runs. Its contents are fixed, shipped, and not authorable:

1. **Live sessions are killed** — everywhere, on every surface, immediately. A policy that cannot instantly end every session for a named human is missing its most important verb.
2. **Tokens, API keys, and device trust the person created are revoked**, including anything they created for a robot.
3. **Shared credentials the person could have known are flagged for rotation** — each as a dated task with an owner, a *what breaks if this dies* note, and a test-before-switch step, reusing **Credential And Certificate Expiry Radar** rather than a second inventory.
4. **Every open thread they own is reassigned to a named person**, one dated transition per thread, never a bulk null and never an orphan — because the Thread contract's owner may not be empty at any instant.
5. **Every robot they supervise is re-supervised or retired.** The switch **will not complete** until each one has a living supervisor; an unsupervised automation with valid credentials is the failure this rule exists to prevent.
6. **They leave alert routing, the huddle's who's-in list, and tomorrow's capacity** — so the day's rendering is correct the next morning without a second edit.
7. **A dated termination-of-access record is filed**, listing all six of the above with times, and *that record is the evidence* — a page in the binder produced by an action the practice had to take anyway (FR-4, FD-6).

What the switch does **not** do is as load-bearing as what it does. **It does not delete the person.** Their name stays on every transition they ever made, forever; history is never rewritten (FD-3). And its governance tier is honest: **tier 2, do + notify** — it executes immediately and appears in Today and in the close — but **reversal is a rehire, not a rewind**. The rotated credential cannot be un-rotated, and the contract's "did the effect leave the building" flag is what lets the undo surface say so plainly instead of promising something it cannot deliver.

**The same switch runs for four populations**: a person, a robot, an external biller whose contract ended, and a support grant that expired. One mechanism, four kinds of leaving, one shape of evidence.

### 4. Absence covers; departure reassigns

The distinction is the whole design, and it is what the delegation subsystem was obscuring.

**Marking yourself out is one control** — *I'm out, these dates* — with four consequences and no objects:

- **Capacity withdraws** for those dates on those locations, which is what turns a clinician's absence into the mass-reschedule work (**Provider Absence And Mass Schedule Disruption**) rather than into twenty-two silently double-booked slots.
- **Your owned threads pool** — visible to everyone, claimable by opening — **and your name stays on them**. Ownership is not rewritten, because rewriting it destroys the only fact the return briefing needs.
- **A cover may be named** for the dates. The cover is a fact about a day, not a grant: coverage never adds access a recipe did not already carry, which is why it is not break-glass and needs no review.
- **Your recurring obligations surface on the huddle page** flagged *Dana's, and she's out*.

Unplanned absence is the common case — the 7:40am sick call — and it is the same control, entered at 7:41am. There is no planned-out-of-office schedule to maintain and no acceptance handshake.

**The return briefing (*while you were out*) falls out for free**, and only because ownership was preserved: what closed, what your cover did and on which threads, what is still yours, and what aged past its clock while you were gone. It is a rendering, not a report — three lists over threads you still own, filtered by transitions made in your absence.

**Departure is the other case, and it does rewrite ownership** — one dated transition per thread, in the switch above. That is the correct asymmetry: absence is temporary and the practice needs to know whose it was; departure is permanent and the practice needs to know whose it is now.

*One absence model, four consumers* — staff coverage, provider availability, capacity rendering, and practice closure days. Two concepts here would be a defect, not two features.

### 5. Access that expires without anybody remembering

The temp, the per-diem, the locum, and the vendor's support engineer are the same object: **a roster entry with an end date set at the moment it is created**. Nothing else is required, no reminder is scheduled, and no one has to act on the last day — the row simply stops working, which is the only revocation mechanism that is reliable at this staff size.

Two of them carry a little more:

**The locum's two clocks are different clocks, and that is the roster/credentialing seam made visible.** Their roster end date says when they stop being able to open the software. Their Q6 substitute-billing day countdown says how much longer their work is billable under the absent physician. These are about the same person and the same absence and they are not the same number; **Substitute And Supervising Billing Arrangements** owns the second one. If the two are ever collapsed into one field, both will be wrong.

**Support access is a temp with a consent artifact.** No openPractice or MSP engineer sees anything without a **practice-granted, time-boxed, fully logged grant**: the practice names the window ("until 5pm today"), the grant creates a roster entry with the support recipe and that expiry, every action inside it is attributed to the engineer by name and shown back to the practice when it ends, and the diagnostic bundle that accompanies it is **PHI-scrubbed by construction** rather than by policy. Because a correlated thread history resolves to a patient's whole financial life, it is exactly the object that must never appear in a diagnostic bundle, and the scrub is asserted inside the bundle rather than promised outside it. The grant is tier 2 — it executes at once and the physician-owner is notified by text — because an approval gate on support access is a gate the office manager will route around at 9pm on a Saturday, and the unsanctioned route loses the event entirely.

### 6. The external biller is on the list

Marcy at Ledgerline does the practice's work under its authority, so she is a roster entry (FD-1) — while the interpreter vendor, the referring office, the bank, and the payer's enrollment department are not, and appear instead as Promise makers, Money Line sources, and Thread counterparties. The distinction is *whose authority the work is done under*, and it is the only test needed.

Being on the list gives her three things the practice currently gets from nobody:

- **Bounded views** — her recipe, above. She works threads; she does not hold the practice.
- **Her own accountability** — she is a Thread owner like anyone else, so her work sorts into the same list by dollars × clock, her stuck threads are visible, and "what is Ledgerline actually doing" is a filter rather than a phone call.
- **Her work visible to the practice**, which is the half every incumbent omits: the practice can see what she closed, what she has not touched, and what aged out under her name.

Two constraints ride with her. **Her roster entry cannot activate without a linked, dated, unexpired BAA** — this is **BAA-Gated Vendor Activation** applied to a person instead of a connector, and it is the same control: the practice cannot accidentally create an unpapered PHI relationship because the software will not let it. And **her contract end date is her end date**, running the same switch, which is what closes the review's "no offboarding when the contract ends" gap without a second mechanism.

### 7. Every robot is on the list too

**Automations as employees.** Each automation is a roster entry with a **job title** from a shipped list, a **human supervisor**, a **start date**, and an **end date**. It inherits the entire access, attribution, coverage, review, and offboarding apparatus already built for humans, and the office manager manages it with a mental model she already has.

The shipped machine jobs are named as jobs, not as scopes: **Reminder Sender**, **Eligibility Checker**, **Claim Submitter**, **Remit Poster**, **Statement Sender**. The job *is* the boundary, so **there is no scope designer** — nobody at this practice can evaluate `appointment:read`, and an approval nobody understands is not an approval. What the recipe card says is what the office manager judges: *can send patient texts · can never see balances · can never move money.*

Four properties come with the framing rather than being added to it:

- **Every automated action is visibly attributed to the machine actor by name** in the record's history, so staff can see that the reminder robot sent that message and not a colleague.
- **A robot whose supervisor leaves cannot keep running** — the offboarding switch blocks on it (effect 5 above).
- **Every robot has an end date**, which is why unused automation dies on its own rather than by anyone remembering to revoke it.
- **One all-stop.** A single visible button stops every robot immediately, for when something is behaving strangely at 2pm on a Tuesday. It is the practice's answer to a machine-speed blast radius, and it is a button, not a procedure.

Consequential actions remain governed by FR-6 wherever they happen, not by who holds them: a robot with the Claim Submitter job still submits at tier 3, behind the visible cancel window, exactly as a human does. **The roster decides who may act; the tiers decide how the act is governed.** Nothing about being a robot changes the second half.

### 8. The roster renders the Day

Capacity is a rendering of this list (C-1): roster entries as **person × location × working hours**, projected onto a Day. An **open slot is roster time with no Thread on it**. Saying this explicitly is what prevents the first person to build the booking surface from inventing a Schedule noun and a second workforce system that then disagrees with this one about who is out on Thursday.

The Day consumes the roster four more times:

- **Who huddles** — the morning page prints *who is in and out* straight from the list, which is why the out-flag must exist before the huddle does.
- **Who is at the desk** — the Pile's default owner is *whoever is at the desk this morning*, and that must resolve to **a name**, because the Thread contract forbids a null owner. The roster resolves a duty position to a person for a day; the Pile never owns a shift.
- **Who covers** — the coverage fact for those dates, printed beside the flagged obligations.
- **Who signs the close** — the End-of-Day Close sheet carries a named signature from this list, and an unsigned close carries the name of whoever should have signed it. That signature is what makes the sheet evidence rather than a printout.

### 9. The access review is exhaust, not a campaign

Quarterly, one screen: every row on the list, showing person or robot, job, last login, end date, and whether their recipe union carries a named acknowledgment. Signed and dated in under three minutes. **That is the access review**, and it is the only form of it that will ever be completed at this practice size.

It costs three minutes because everything in it already happened for other reasons: the offboarding records, the expired temps, the support grants and what was viewed inside them, the compensating-control acknowledgments, and the rotation tasks the last departure created. Each is a page in the binder that nobody prepared. Access-review *campaigns* — assignment, reminders, escalation, attestation workflow — stay deferred, and a signed roster page is not one.

### Rhythm and clearing conditions (FR-5)

| Cadence | What the roster does | Clears when |
|---|---|---|
| **Day** | prints who is in and out on the huddle; resolves the desk to a name; carries the close signature | the day closes with a signature |
| **Week** | *The Practice* shows every roster fact that will not be true in fourteen days — an ending temp, an expiring BAA, a support grant still open | each line has a name and a date on it |
| **Quarter** | the roster page, read and signed in three minutes | signed; the page files itself |
| **Year** | nothing of its own — credential revalidation and training belong to the credentialing lifecycle, not here | *(not this document's)* |

### Governance tiers on roster actions (FR-6)

| Action | Tier | Note |
|---|---|---|
| Add a person; change locations; mark out; name a cover; extend a temp | **1 — do + undo** | reversible, silent, undoable from the record |
| Change a person's job (their recipe union); grant support access; **run the offboarding switch** | **2 — do + notify** | executes at once; appears in Today and in the close; the physician-owner is texted on a support grant. Offboarding's reversal is a rehire, not a rewind |
| *(none native to the roster)* | 3 — delay + cancel | nothing on this list leaves the building; outbound acts are governed where they happen |
| End-dating the last holder of the practice-manager recipe | **4 — hard stop** | refused until a successor is named; the practice may not lock itself out of its own list |

Money-moving actions are governed at the action, not at the grant. A refund is tier 4 because it is a refund, not because of who holds the recipe — which is why the recipe layer needs no second approval gate and does not have one.

## Interfaces and boundaries

**Consumes.** The practice's own payroll register and legacy user list (FR-8, so the list is imported rather than authored); the location register; twelve months of actual bookings, from which working hours are derived rather than typed; the BAA registry, for external participants; and the practice's own consent, for support grants.

**Emits.** Roster references, which are the only legal value of a Thread's `owner` (N-1) and of the actor on every transition. The grant set enforced below every entry point. Capacity, as person × location × hours projected onto a Day. The out-flag and its cover, to the huddle, to capacity, and to the mass-reschedule path. Six artifacts, each named with what it saves (FR-4): the **dated termination-of-access record**; the **credential-rotation list** an offboarding produces; the **compensating-control acknowledgment**; the **support-grant receipt** showing what was viewed; the **while-you-were-out briefing**; and the **signed quarterly roster page**.

**Named valves (FR-3).** Every verdict here names what changes when it changes: *roster entry with a recipe* → the grant set at the data layer; *end date reached* → sessions killed, tokens revoked, rotation tasks created, threads reassigned; *out-flag set* → capacity withdrawn, threads pooled under a named cover, obligations flagged on the huddle; *temp expiry* → access simply stops; *missing or expired BAA on an external participant* → the roster entry will not activate; *consent grant expired* → the support session ends; *supervisor departs* → the offboarding switch blocks until the robot is re-supervised or retired; *dangerous recipe union* → a dated acknowledgment filed to the binder.

**The boundary with credentialing, stated once.** **The Roster answers whether a person may open the software — employment facts. Credentialing answers whether a provider may be billed under, for this payer, at this location, on this date — billing facts.** A provider can be fully and correctly on the Roster and entirely unbillable, and that is not an access error: it is a **held claim** with an enrollment case behind it. The two share exactly one thing — a person — and one date-shaped coincidence, the locum's two clocks. [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md) owns credential evidence, enrollment cases, directory assertions, and administrative readiness; this document owns none of them and must never store a license, an NPI-payer pair, or an effective date.

**What this document does not own.** It does not own the audit ledger (it supplies the actor); the Thread (it supplies the owner); the Day (it supplies who is in, who is at the desk, and who signs); the credential and certificate inventory (it triggers rotation tasks into it); the BAA registry (it consumes a gate from it); or any money authority (tiers govern actions, not people). It does not model counterparties — the interpreter vendor, the referring office, the bank, the payer's enrollment department — and it does not model patients, guarantors, or guardians, which are party-register facts (A-4).

**The Frappe/bench administrator is outside this list, and the honest move is to say so.** It is the most privileged identity in the deployment, is typically shared with whoever set up the server, and is not governed by anything the app enforces on itself. The shipped default is that **Desk is disabled for practice staff entirely** and reserved for support identities under a consent grant, which removes an entire class of enforcement risk; what remains uncovered is stated in the binder's gap list rather than implied away.

## Alternatives and tensions

**Recipes that compose but never edit will strain, and the strain is predictable.** Somewhere there is a practice whose front-desk person must also post remittances but must never adjust a balance, and no union of shipped recipes expresses it. The alternative — allow one subtraction — reopens the matrix as an accretion, undetectably, one row at a time, and the review's whole finding is that the matrix is the wrong surface. The design takes the strain deliberately, and the escape is to **add a shipped recipe in a release**, not to edit one in a practice. If the beta practice needs a recipe the product does not ship, that is a product finding worth having.

**A hard stop on the embezzlement triangle would be safer and is not shippable.** The review proposed one; FR-1 rules that a gate requiring a nonexistent second human is mis-framed. The acknowledgment is weaker in theory and stronger in practice, because it will still be there in month six. If the beta practice's acknowledgment is signed and then never reviewed, the control was theater and should be measured as such.

**Absence keeping ownership is a choice with a cost.** A thread whose owner is out for nine days keeps a name on it that cannot act, and a naive reading of the stuck list will blame Dana for work she could not do. The mitigation is that the cover is displayed beside the owner wherever the owner is displayed, and that a thread pooled during an absence is not counted against its owner's aging. The alternative — reassign on absence — is simpler for the day and destroys the return briefing entirely, which is the higher-value half.

**Capacity as a pure rendering will be argued at the first template.** A practice that wants Tuesday afternoons blocked for paperwork is expressing something that is neither a roster fact nor a Thread, and the temptation to add a Schedule object will arrive in week three. The frame's answer is that a recurring block is roster time with a standing non-visit Thread on it, and that answer should be tested against the design partner rather than asserted.

**Robots as employees is a better abstraction and a leakier one.** A robot has no locations, no working hours in any human sense, and no absence; four of the six employment facts are ceremonial for it. The framing earns its place because it delivers offboarding, attribution, supervision, and expiry for free, and because the office manager can reason about it — but the ceremonial fields will invite someone to give them meaning, and they should stay empty.

**One list means one list, including the growth case.** FR-1's watch-out cuts both ways: the frame governs surfaces and defaults, never schema. Fifteen staff should mean more rows and possibly more shipped recipes, never a redesign of the roster entry. If growing the practice would require a new object, the ceiling has leaked into the schema and this design is wrong in the other direction.

## Open questions

- **Which duty positions must the roster resolve, and how?** The Thread contract forbids a null owner, and the Pile's default owner is *whoever is at the desk this morning* — a shift, not a person. Resolving that from working hours is straightforward on a normal Tuesday and undefined on a day when two people are out and a temp is at the desk. Decide before the Pile ships, because a thread that mints with no owner is a contract violation on day one.
- **How does a person hold different recipes at different locations?** The list has one job per person, and a two-site practice may genuinely have someone who is front desk at one site and manager at the other. Composition currently unions across all locations, which over-grants. Whether the fix is a per-location job or a rule that composition is location-scoped is unresolved, and it is the most likely first strain on the "no editing" rule.
- **What happens to the offboarding switch when the person leaving is the only practice manager?** The tier-4 refusal names a successor, but a practice of three where the manager quits on a Friday has no obvious successor, and the failure mode — nobody can offboard her — is worse than the risk. A break-glass shape for exactly this case may be required, and it is the one place emergency access might genuinely earn its keep.
- **Is the compensating-control acknowledgment reviewed, and by whom?** It asserts that *the practice owner reviews the adjustment report monthly*. If nothing verifies that the review happened, the acknowledgment is a signed statement of an untruth, which is worse than no control. The candidate valve is that the monthly review's absence appears on The Practice as a named line.
- **How is a shared front-desk workstation modelled without shared credentials?** The design is a long device session plus a fast per-user PIN or badge unlock plus full per-user attribution, and it is the single most common small-practice reality that generic session policy gets wrong. It is named here and owned by identity, not by the roster — but if it is not solved, the roster's attribution guarantee is false at the busiest desk in the building.
- **What does the return briefing do about work that was neither done nor covered?** Three lists are easy; the fourth — *this aged past its clock while you were out and nobody touched it* — is the one that matters and the one that will read as an accusation. Whether it belongs in the briefing or on the huddle is a tone decision with real adoption consequences.
- **Does the support grant need a second signature at a two-person practice?** The physician-owner is texted, but if the office manager grants access to herself-as-vendor at 9pm there is no second party. The review's own finding is that self-review is theater; the honest options are to accept it and log it loudly, or to require the owner's tap and accept that it will occasionally be unavailable.
- **What is the roster's behavior across the migration seam?** The legacy system's users map to recipes imperfectly, and day one either over-grants everyone or blocks half the staff. Whether the import proposes recipe matches for confirmation, or the practice simply retypes six rows, is unresolved — and six rows may honestly be the cheaper answer.

### Discrepancies recorded against the binding inputs

- **A citation defect, not a conflict.** [The Visit Thread](openpractice-small-practice-beta-visit-thread.md)'s five-properties table reads *"A robot is a roster entry (FD-5, the roster model)"*. FD-5 is the governance-tier decision; the roster model and the first-class external biller are **FD-1**. Substance is unaffected; the citation should read FD-1 at the next edit of that document.
- **The Pile's owner is a shift, not a Roster entry.** The same document requires that owner is *always* a Roster entry and never null, then defines the unmatched-inbound-page owner as *"whoever is at the desk that morning"*. Both cannot be literally true. Resolved here in the roster's favor — the roster resolves a duty position to a name for the day — and raised as the first open question above.
- **A sequencing split this document proposes.** [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) places **The Roster** and the **Same-Day Offboarding Switch** in Slice 0 as beta-blocking, but places **Coverage Day And While-You-Were-Out** and **Provider Absence And Mass Schedule Disruption** in Slice 5 at priority V. Absence and coverage are one mechanism with the roster, and the huddle page prints *who is in and out* from Slice 0 — so the **out-flag and its capacity consequence belong in Slice 0**, while the return briefing and the mass-reschedule path can stay in Slice 5. Recommended as an amendment rather than asserted.
- **"Access review" appears in two lists with opposite dispositions.** FR-5's quarterly rhythm in [Feature Normalization](openpractice-small-practice-beta-feature-normalization.md) includes an access review; scope-and-sequencing's post-beta list defers **access-review campaigns**. Read as consistent: a roster page signed in three minutes is not a campaign, and the campaign machinery — assignment, reminders, escalation, attestation workflow — stays deferred.
- **A deliberate deviation from the source review.** [Platform And Trust](../review/openpractice-feat-review-platform-and-trust.md) §1 proposes a **hard stop at the moment of grant** for the money triangle. This document permits the union and files a dated acknowledgment instead, because FR-1 forbids a gate that depends on a second human who does not exist. Recorded here so the deviation is a decision rather than a drift.

## Relationships

Owns the sixth noun of the work model in [Feature Definition And Catalog Normalization](openpractice-small-practice-beta-feature-normalization.md) Part 1, and the register entries **The Roster**, **Same-Day Offboarding Switch**, and **Support Access With Consent**; exercises FR-1 (the frame that makes recipes shippable), FR-2 (no *permission*, *scope*, or *provisioning* on any surface), FR-3 (eight named valves), FR-4 (six artifacts), FR-5 (the day/week/quarter rhythm), FR-6 (four tiers, one hard stop), and FR-8 (the payroll register and twelve months of bookings before any authored field).

Supplies the `owner` that [The Visit Thread](openpractice-small-practice-beta-visit-thread.md) requires on every thread and the actor on every transition (N-1), and receives from it the rule that ownership is never null — which is why departure reassigns per thread and absence does not.

Renders into [The Day: Huddle, Close, And The Practice Rhythm](openpractice-small-practice-beta-day-rhythm.md) as who is in and out, who is at the desk, who covers, and who signs the close; capacity is this list projected onto a Day (C-1), which is why no Schedule object exists.

Bounded against [Enrollment-Gated Billing And Retro-Release](openpractice-small-practice-beta-enrollment-gated-billing.md) and [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md) by one line: **employment facts gate access; billing facts gate claims.** A provider fully on this list may be entirely unbillable, and the consequence is a held claim, not an access error.

Replaces the surfaces proposed in [Platform Security And Compliance](openpractice-feature-list-platform-security-and-compliance.md) feat 1 (the minimum-necessary authority designer) and feat 5's access-review campaigns, while keeping that document's grant model, field-level protection, and tamper-evident ledger intact underneath; supplies the actor and the termination record that its evidence artifacts consume.

Consumes the BAA gate from and shares the rotation inventory with the platform capabilities sequenced beside it in Slice 0 of [Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md) — **BAA-Gated Vendor Activation**, **Credential And Certificate Expiry Radar**, and **Test/Production Firewall** — all of which risk **R-3** moved into beta-blocking scope when FD-8 put real patients and real money into the beta.

Replaces the delegation, routing, and escalation machinery graded down in [Operations And Intelligence](../review/openpractice-feat-review-operations-and-intelligence.md) A2–A4, and implements its **Coverage Day and While-You-Were-Out** missing feat; derives its central reframe — *payroll is the access control system* — and the offboarding, support-consent, and automations-as-employees designs from [Platform And Trust](../review/openpractice-feat-review-platform-and-trust.md) §1, §11, §12, and its cluster-level missing-feat list. Governed throughout by [Framing Decisions](../review/openpractice-framing-decisions.md) FD-1 (the frame and the first-class external biller), FD-5 (the four tiers), FD-6 (evidence as artifacts), FD-3 (nothing is ever rewritten), and FD-7 (the thread contract this list supplies owners to). Modifies no existing document; the amendments it proposes are recorded above rather than made.
