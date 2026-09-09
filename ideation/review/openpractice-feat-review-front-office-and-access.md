# Feat Review: Front Office And Access

Status: brainstorm
Kind: review
Summary: Fifty-three front-office feats reviewed against a 2-4 person ambulatory front desk finds a strong identity-readiness-consent spine buried under seven vendor-shaped product lines, with the app, AI, and sensor layers largely deferrable, the phone and the walk-in entirely missing, and the highest-leverage move being to collapse the whole cluster into one day view where every appointment is either Ready or names the one person who owns the one missing thing.
Topics: openpractice-feat-review, front-office
Captured: 2026-08-26

## How to read this review

Fifty-three feats across seven atomic documents plus the cluster synthesis. Verdicts are assigned to the feat **as written**; where the underlying principle deserves a different verdict than the mechanism proposed, the justification says so explicitly and the reframe carries the principle to a cheaper home.

The reviewing lens throughout: **a 2-4 person front office at a 1-3 provider ambulatory practice, 20-40 visits a day, no IT staff, one person answering the phone while another checks someone in.** A feat earns "essential-core" only if that office would notice its absence within a week. It earns "defer" if the office would not notice its presence within a year.

Verdict vocabulary:

- **essential-core** — the beta is incoherent or unsafe without it.
- **differentiator** — competitively distinguishing and worth beta cost.
- **valuable-enhancer** — real value, but the beta survives a thin version.
- **questionable** — the stated cost/benefit does not close at this scale, possibly ever.
- **defer** — good idea, wrong phase; a prerequisite or a cheaper substitute exists.

---

# 1. Registration And Demographics

## A1. Guided registration intake

**Verdict: essential-core.** A three-person desk cannot afford to show a 60-field form to a 42-year-old established commercial patient, and cannot afford to *miss* the guarantor question on a 9-year-old. Adaptivity here is not a luxury, it is the difference between 90 seconds and 6 minutes at the desk. The risk is that "adapt required steps to age, payer, guarantor, location, and appointment context" becomes a rules-authoring project the practice cannot maintain — which is how this exact feature fails at every incumbent.

**Make it more useful.** Ship four canned intake profiles rather than a rule builder: *adult commercial*, *adult self-pay/uninsured*, *minor with guardian*, *Medicare with secondary*. Auto-select by DOB + coverage type and let staff switch profiles with one control. Override reasons come from a five-item picklist, never free text, and expire at the next visit rather than persisting silently forever. For established patients, replace the wizard entirely with a single-screen "still true?" confirmation and a "what's missing" strip — a wizard is for strangers, and most of the day is not strangers.

**Alternate/better solutions.**

1. *Simpler mechanism:* delete the adaptive engine. Use one flat registration form where fields self-hide via native Frappe `depends_on` conditions, and compute a read-only **Registration Completeness** checklist beside it. Requiredness becomes advisory at intake; the only hard gate lives at the billing boundary (A6). Nothing to author, nothing to version, nothing to break.
2. *Derive requiredness from the downstream artifact:* the 837P professional claim already encodes exactly which party, identifier, and date elements are mandatory for a given payer and service. Generate the required-field list from the claim schema plus the payer profile rather than maintaining a parallel registration rule set. Requiredness then cannot drift from reality, and a payer edit updates intake automatically.
3. *Copy-forward as the default surface:* for anyone with a prior visit, the intake path is a diff, not a form. The only "guided" logic needed is "which blocks are stale?" — a recency rule, not a decision tree.

**Reframe.** Not "adapt required fields to context" but: **never ask a question whose answer we already have or will never use; the claim decides what is required, and the patient's history decides what is asked.**

**Gaps it leaves.** Unknown-identity walk-in (a John Doe record that must later merge cleanly). Patients who decline SSN, race, or ethnicity — refusal is a valid answer and needs a value, not an empty field. No fixed address, shelter address, or general delivery. Foreign addresses and non-US phone formats. Workers' compensation and motor-vehicle-accident visits, which introduce an employer, adjuster, claim number, and date of injury that no demographic profile contains. Deceased patients with an estate guarantor. Legal name changes mid-course. Patients registering for a family member who is not present.

## A2. Identity-safe record creation

**Verdict: essential-core**, with the identity-verification-adapter half cut. Duplicate records are the top silent cost in a small practice: split balances, two statements, a claim under the wrong record, an eligibility check against a stale coverage row. But commercial identity proofing (knowledge-based authentication, credit-header matching) is an enterprise procurement a three-person office will never buy, and "route uncertain matches to a merge-adjudication queue" invents a queue nobody staffs.

**Make it more useful.** Move the whole intervention to *creation time*, not to a queue. The instant someone types last name + DOB, show near-matches inline with enough disambiguating context to choose — last visit date, provider seen, phone last four, address city. Make "create new anyway" require a reason from a picklist. Replace merge *approval* (which needs a second person a small practice does not have) with merge *reversibility*: any merge is undoable for 30 days with a full restore of both records and everything attached. Undo is cheaper than governance at this scale and strictly safer than an unstaffed adjudication queue.

**Alternate/better solutions.**

1. *Simpler mechanism:* a soft-unique convention instead of a scoring model. Treat `(normalized last name, DOB, last four of any known phone)` as a soft key; a collision opens a chooser rather than a score. The overwhelming majority of real-world duplicates share DOB exactly; probabilistic scoring buys the long tail at the cost of a model, a threshold, and a queue.
2. *Prevent instead of detect:* close every unattended record-creation path. Patients reach the system through an appointment link, a referral record, or a staff-created record — never through open self-registration. If no anonymous actor can create a patient, duplicates become a staff-only, on-screen, one-time decision.
3. *Let the payer resolve identity:* the 271 eligibility response carries the payer's own member identifier. For any returning patient, matching member ID resolves identity before a single demographic field is compared — and it resolves the married-name and typo cases that name matching gets wrong.

**Reframe.** Not "detect duplicates after they exist" but: **no path may create a patient record unattended; every new record is a deliberate human act taken with the near-matches already on screen — and every merge is undoable.**

**Gaps it leaves.** The entire post-merge aftermath: appointments on both records, in-flight claims, an outstanding balance on the loser record, signed consent forms attributed to the wrong ID, previously sent statements, a live message thread, an issued previsit link. Twins sharing surname, DOB, and address — the case where every heuristic is wrong. Name changes from marriage or gender affirmation that present as duplicates and must not be merged away. A wrong merge that must be *split*, which is not the inverse of a merge. A patient who is also a staff member. Duplicates created in openChart and propagated inward across the FHIR boundary — the identity contract runs both directions and only one is described.

## A3. Coverage capture from cards

**Verdict: valuable-enhancer**, and the OCR half is close to questionable. Card OCR across thousands of payer layouts is genuinely unreliable, and staff typing a member ID takes about twenty seconds. The durable value in this feat is the *image as evidence* — for appeals, for payer disputes, for proving what the patient presented — not the extraction.

**Make it more useful.** Split the feat. Ship image capture attached to the coverage record with effective-date scoping, first and cheaply. Then make the capture trigger an immediate real-time eligibility check using whatever fields are present, and let the **271 response correct the fields** rather than asking a human to correct OCR. The payer's answer is authoritative; pixels are not. Show the patient or staff the eligibility result as the confirmation step — that is a satisfying outcome, whereas confirming OCR guesses is unpaid data-entry labor.

**Alternate/better solutions.**

1. *Simpler mechanism:* no OCR at all. Payer chosen from a searchable list, member ID typed, then verified by 270/271 within seconds. A wrong entry surfaces as a payer rejection immediately, at the moment the person who can fix it is still present.
2. *Card-image-as-evidence-only:* store the image as a document artifact bound to the coverage row's effective period; never parse it. This delivers the appeals and dispute use case at roughly five percent of the effort and carries none of the confirmation-UX burden.
3. *Skip the card entirely:* coverage discovery by demographics through the clearinghouse, or reconstruct coverage from the most recent 835 remittance, which states exactly who paid, under what plan and group. For a returning patient this is a strictly better source than anything the patient can photograph.

**Reframe.** Not "read the insurance card" but: **the card is a hint, the payer is the authority — no coverage field becomes authoritative until a 271 confirms it.**

**Gaps it leaves.** Medicaid cards carrying almost no usable data. Coordination-of-benefits ordering when two or three coverages exist. Medicare Advantage cards that look commercial and bill differently. Cards where the subscriber is not the patient — the card shows the subscriber, and the relationship is the missing field. Coverage terminated but the card still in the wallet. Cards with a scannable barcode or QR, which would beat OCR outright and is not mentioned. Patients with no card at all, who are the exact population the equity commitment covers. Card images as a retention and deletion liability with no stated schedule.

## A4. Relationship-aware billing identity

**Verdict: essential-core.** This is the schema decision the rest of the cluster silently depends on. Get it wrong and minors, divorced parents, adult dependents, guardianship, and confidential services become unrepresentable — and it is the single most expensive thing on this list to retrofit after a year of production data. Cheap now, catastrophic later.

**Make it more useful.** Model it once, then hide it. Adults default to "patient is subscriber" and "patient is guarantor" as pre-checked boxes staff never touch. Selecting a guardian for a minor auto-populates guarantor and communication recipient in one act. Render the whole thing as a single **"Who's involved"** card on the patient page — five roles, each with a name and a date range — rather than five linked doctypes the receptionist must navigate. The model should be invisible in the 85% case and complete in the 15% case.

**Alternate/better solutions.**

1. *Simpler mechanism:* one `Party Role` child table on the patient — `(role, person, valid_from, valid_to, evidence, source)`. One table answers every question, effective dating is free, history is inherent, and there is no join graph to reason about.
2. *Model the household/billing account as the financial anchor:* a guarantor plus their members. Family statements, family balances, family visit blocks, and one signature session for three children all fall out for free — and it matches how a family actually experiences the practice, which the person-centric model never does. This is the single highest-value structural alternative in the whole cluster.
3. *Adopt X12's subscriber/dependent model verbatim as the internal model.* No translation layer at claim time, no impedance mismatch, no "our guarantor is their subscriber" bugs. Convention replaces design.

**Reframe.** Not "model relationship roles" but: **every dollar and every message has exactly one accountable person at any point in time, and that person changes on known dates — store the dates, derive everything else.**

**Gaps it leaves.** Divorced or separated parents with alternating financial responsibility and a court order that governs it. The 17-year-old turning 18 mid-course of treatment, where guarantor authority, communication routing, and confidentiality all change on a birthday and nothing is described as automatic. Emancipated minors. Confidential minor services (Title X, STI, behavioral health) where the guarantor must *not* receive a statement, and where the payer's own EOB will disclose it anyway — a limit the practice cannot control and must at least be able to warn about. Foster care and state custody. Power of attorney and guardianship documents, their evidence, and their expiry. A guarantor who dies. One person acting as guarantor across two unrelated families.

## A5. Privacy-aware front desk

**Verdict: valuable-enhancer** for the beta, with one essential-core piece inside it. In a three-person office everyone can already see everything, and break-glass is theater with no one to review the alerts. But **contact restrictions** — do not call the house, do not leave a voicemail, mail to this address instead — are violated constantly, cause real harm, and belong in beta.

**Make it more useful.** Split the feat. Ship a `Contact Restriction` primitive enforced inside the communications send path (essential); defer VIP flags, break-glass, and aliasing. Enforcement matters more than display: a restriction that only renders as a banner will be violated by an automated cadence at 9am. Render it as a red header strip on the patient record *and* make it a deny in the consent evaluation (C3), so it cannot be bypassed by any surface.

**Alternate/better solutions.**

1. *Simpler mechanism:* one restriction object shaped `(person, channel, address, purpose, allow|deny, reason, expires)`, evaluated by the same function every send path already calls. No VIP concept, no aliasing, no access-control machinery.
2. *Restrict by purpose, not by person:* the sensitive thing is usually the visit, not the patient. An administrative purpose code derived from appointment type ("behavioral health follow-up") can suppress detail in outbound messages without openPractice knowing anything clinical — a cheaper and more honest boundary than tagging patients as sensitive.
3. *Conventions beat access control at this scale:* a practice-wide default of "never state the practice name or visit reason in a voicemail or SMS body" plus a "call me by" nickname field delivers most real-world confidentiality with zero permission model. The link-not-content pattern in C5 already does the rest.

**Reframe.** Not "restrict who can see the record" but: **the patient tells us once how they may and may not be reached, and no automated path may contradict that instruction.**

**Gaps it leaves.** Physical-world exposure the software cannot touch — paper sign-in sheets, calling full names into the waiting room, a monitor visible from the lobby. Restrictions added mid-cadence when messages are already queued. Staff searching by phone number and surfacing a restricted patient incidentally. Restrictions surviving a merge. A guarantor's legal right to billing information colliding with a minor's confidentiality. Nothing describes how a restriction reaches openChart, which will happily call the house.

## A6. Billing-readiness gates

**Verdict: differentiator.** This is the best structural idea in the packet and the mechanism that converts a reactive front office into a proactive one: a single list that says "these six appointments will fail, and here is why." The differentiation is that readiness is a *contract consumed by other domains*, not a report someone runs. Nothing at this price point does this.

**Make it more useful.** Never render readiness as a separate queue — put it on the schedule row where the work already lives. One glyph per appointment (green / amber / red) with the named blocker on hover, and **each blocker carrying a one-click resolution**: send the intake link, run eligibility again, open a call script. Never hard-block; always warn plus require a reason, because the clinical reality is that the patient will be seen anyway. Express readiness *per downstream operation* (ready-to-verify, ready-to-estimate, ready-to-claim, ready-to-statement), because a record can be perfectly claimable and completely unstatementable.

**Alternate/better solutions.**

1. *Simpler mechanism:* derive readiness by dry-running the actual downstream operation. Build the claim in memory; if it validates, the record is claim-ready. There is no separate rule set to author, and readiness can never drift from what the operation actually requires — the classic failure of every rules-based completeness feature.
2. *Readiness as a countdown, not a gate:* a T-3 daily digest — "4 of tomorrow's 22 appointments are not ready" — driving outbound calls. This is literally what a good office manager already does on paper, and it converts readiness from a state into a worklist.
3. *Hand the blockers to the patient:* the readiness blockers *are* the previsit task list (E1). One computed object serves the staff worklist and the patient checklist, which eliminates an entire class of divergence between what staff think is missing and what the patient is asked for.

**Reframe.** Not "gate downstream operations on registration completeness" but: **at any moment, every future appointment should be able to state in one sentence what would make it fail and who owns fixing it.**

**Gaps it leaves.** Same-day add-ons and walk-ins, where pre-service readiness is structurally impossible and the concept needs a compressed variant. Readiness defects belonging to the *guarantor* rather than the patient. Blockers caused by the payer rather than the practice (portal down, plan not contracted, no eligibility response) — different owner, different remedy. Who may grant an exception, for how long, and whether it survives a reschedule. Readiness decay: verified six months ago is not verified. Services that legitimately need almost no readiness (a self-pay flu shot). No stated interaction with the clinical reality that the visit happens regardless.

---

# 2. Scheduling And Patient Access

## B1. Rules-as-data booking

**Verdict: essential-core mechanism, questionable as presented.** Every practice needs "new patient, 40 minutes, Dr. A, Tuesdays only, not after 3pm, requires the ultrasound room." That is not optional. But "versioned, testable configurations" spanning appointment type, provider, location, resource, duration, patient class, lead time, and prerequisites is a rules language, and a rules language in a three-person practice becomes either unused or unmaintainable within a quarter.

**Make it more useful.** Fix the primitive set at roughly six and refuse to grow it in beta: *type → duration*, *provider × type allowlist*, *day/time availability template*, *lead-time min and max*, *required resource*, *patient class allow (new/established)*. Ship a template gallery seeded per specialty so nobody authors from zero. Every rule edit shows a live "here is what this changes on today's schedule" preview. And build the inverse feature that actually matters at the desk: a **"why can't I book this?"** explainer that names the exact rule and offers the nearest booking that satisfies it, because the receptionist is on the phone with the patient right now.

**Alternate/better solutions.**

1. *Simpler mechanism — slot templates instead of a constraint engine:* providers publish named slot templates ("NP", "F/U", "Proc", "Nurse"), and the only rule is which appointment types may consume which template. The calendar itself carries the constraints, there is nothing to version separately, and it matches exactly how small practices already think and talk about their schedules.
2. *Infer the rules from history:* propose constraints from twelve months of actual bookings — "you have never booked a new patient after 3pm; enforce that?" — instead of asking staff to author rules they have never had to articulate. Configuration becomes review, which small practices can do.
3. *Permit-and-flag instead of prevent:* allow any booking but mark rule-violating ones visibly with a reason, and report on them weekly. In a two-provider office the doctor is ten feet away and the exception is often correct; governance by visibility costs nothing and never blocks a patient on the phone.

**Reframe.** Not "encode booking constraints as versioned data" but: **the schedule must be able to explain itself in one sentence to the person currently on the phone — and offer the next thing that would work.**

**Gaps it leaves.** Provider time off, meetings, hospital rounds, and vacations, and how those interact with rules — the single most frequent schedule mutation in a small practice, and it is absent. Deliberate double-booking, which is a legitimate and universal practice. Template changes that must not retroactively invalidate already-booked appointments. Holidays and closures. One provider covering another's panel. Room and equipment conflicts across chained appointments. Payer-based slot policies (Medicaid caps), which are real, common, and legally fraught. Nothing describes rule *retirement*.

## B2. Vacancy recovery cascade

**Verdict: differentiator**, and by revenue-per-unit-of-effort the highest-ROI feat in the entire cluster. One recovered slot per day at a small practice is meaningful money, and the labor it replaces (a receptionist working a paper "call if something opens" list) is exactly the labor a compressed-role office does not have. The described machinery — controlled waves, fairness policies, complete offer history — is heavier than the beta needs.

**Make it more useful.** Default hard: on cancellation, text the five best-matched patients a first-come link with a 20-minute life; first booking wins, the rest auto-expire with a courteous message. Ranked-fairness modes become configuration later. The genuinely important default is upstream: **waitlist entries should be created automatically** — from every cancellation, every overdue recall, every "let me know if something opens" note logged during a call, every appointment booked further out than the patient wanted. Staff should never maintain a waitlist, because they will not.

**Alternate/better solutions.**

1. *Simpler mechanism:* publish the vacancy to the self-scheduling surface immediately and text a matched cohort a plain link. The booking record is the only state — no offer object, no expiry timer, no hold, no cascade state machine. Whoever books it, books it.
2. *Standing-slot subscription:* patients declare a preference once ("Tuesday or Thursday mornings with Dr. A") and the system matches against that preference permanently. This replaces a per-cancellation waitlist with a durable demand signal that also tells the practice what capacity it should be adding.
3. *Cross-domain substitution — the recall list is the waitlist:* the practice already owes visits to overdue patients. Filling a vacancy from clinical-necessity order is both better care and better revenue than filling it from a queue nobody curates, and it requires no new data at all.

**Reframe.** Not "recover cancelled slots from a waitlist" but: **an empty slot is a sales lead with a twenty-minute shelf life — who is the best-matched patient we already owe a visit to, and can we reach them right now?**

**Gaps it leaves.** Offers made to patients who cannot be financially cleared in time — a same-day fill needing prior authorization is a guaranteed denial, and no readiness check gates the offer. Double-acceptance races. Offers to patients carrying a large unpaid balance, which is a policy decision nobody has made. Patients requiring an interpreter, who cannot be moved same-day because the interpreter cannot. Transportation-dependent and paratransit patients, for whom a two-hour notice is useless. Providers who wanted the gap. And the counterintuitive one: last-minute fills carry materially higher no-show risk, so recovery metrics that count offers accepted rather than visits completed will lie.

## B3. Risk-shaped attendance strategy

**Verdict: defer.** The pain is completely real — no-shows are the most-discussed number in any small practice — but scoring is not the cheapest cure and this practice cannot support it. One to three providers generate on the order of six to ten thousand appointments a year: thin for a model, thinner per cohort, and the bias-monitoring obligation the doc correctly imposes is work no three-person office will perform. Meanwhile the deterministic substitutes below capture most of the benefit immediately.

**Make it more useful.** If built at all, never show a score. Show *named reasons* from transparent counting rules — "two no-shows in the last twelve months", "new patient", "booked more than 30 days out", "Monday 8am" — because a named reason is auditable, explainable to a patient, and does not require a fairness review. Feed cadence only, never access, never overbooking, and never a fee decision.

**Alternate/better solutions.**

1. *Simpler mechanism — confirm-or-surface:* every appointment must reach an explicit yes or no by T-24h. Unconfirmed appointments become a call list. This needs no model, produces the vacancy list as a by-product, and gives the practice something to *do*, which a risk score does not.
2. *Attack the causes instead of predicting the effect:* lead time is the strongest and most actionable no-show correlate known, and it is fully under the practice's control. Shortening time-to-appointment, adding a same-week option, and making "text CANCEL" trivially easy will beat any model — and an easy cancel path converts no-shows into recoverable vacancies, which is worth more than predicting them.
3. *Use previsit completion as the risk proxy:* a patient who finished their intake three days early is not going to no-show. "Incomplete previsit at T-24h" is a free, causal, unbiased, already-computed cohort that needs no training data and no bias audit.

**Reframe.** Not "predict who will not come" but: **drive every appointment to an explicit yes or no by T-24h, and make the ones still without an answer the day's outbound work.**

**Gaps it leaves.** No distinction among no-show, late-cancel, and practice-initiated bump — three different behaviors with three different remedies, aggregated into one number. Repeat-offender policy, dismissal letters, and their documentation. Cohort bias monitoring nobody will run. The feedback loop where staff overbook on the score anyway, which the doc forbids and cannot prevent. No-shows caused by the practice's own long waits, which the model will attribute to the patient.

## B4. Constrained self-scheduling

**Verdict: valuable-enhancer.** For a small practice the value is *phone deflection*, not patient delight — every self-booked appointment is a three-minute call that did not happen while someone was standing at the desk. But the asymmetry is brutal: a wrongly self-booked visit (wrong type, wrong duration, wrong provider, no auth) costs far more than the call it saved. Narrow it aggressively.

**Make it more useful.** Beta scope: established patients only, two or three appointment types, next 30 days, a provider they have already seen. Everything outside that routes to a callback request that captures preferred windows and a reason — still a deflection, with zero risk. Require a successful eligibility check before the slot is confirmed, so every self-booked visit is financially clean by construction. Show the practice's cancellation policy on the confirmation screen, which is the cheapest no-show intervention available.

**Alternate/better solutions.**

1. *Simpler mechanism — request-to-book:* the patient picks preferred windows, staff confirm in one click from a queue. This removes phone tag without ever creating a bad booking, and many small practices strongly prefer it because the schedule stays under their control. It is also the honest version of "explain when staff assistance is required."
2. *Invite-only scoped links:* instead of an open booking page, send one patient a link offering four pre-approved slots. This is the recall and follow-up path, converts far better than an open page, and eliminates the entire constraint-vocabulary problem because the practice chose the slots.
3. *Book by text reply:* "Reply 1, 2, or 3 to pick a time." No login, no portal, no app, no accessibility audit, and reachable by every patient the practice has a phone number for.

**Reframe.** Not "let patients book within safe constraints" but: **remove the phone call, not the control — the practice picks what is bookable, the patient picks when.**

**Gaps it leaves.** New-patient self-booking, which is simultaneously the highest-value and highest-risk case and is not addressed. Patients selecting a visit type requiring preparation (fasting, holding a medication, a full bladder) — administrative booking of a clinically prepared visit is a real boundary problem. Self-service *cancellation* and how it interacts with a late-cancel policy. Booking several family members. Time zones for telehealth. Screen-reader and low-vision accessibility, which is a legal obligation and appears nowhere in this feat. And the patient who self-books and immediately no-shows, whose behavior differs from staff-booked patients.

## B5. Administrative pathway booking

**Verdict: defer.** Linked sequential holds across steps, locations, and resources is imaging, oncology, and surgical-center shaped. A small ambulatory practice's genuine sequencing needs — a pre-op visit, a procedure, a post-op check — are served by mechanisms an order of magnitude cheaper, and reserved holds actively harm a practice with scarce capacity by stranding slots against steps that may never happen.

**Make it more useful.** If retained at all, reduce to **appointment groups**: N appointments that book, cancel, and reschedule as a unit, with a warning when one member moves independently. No holds, no dependency solver, no partial-failure semantics.

**Alternate/better solutions.**

1. *Simpler mechanism:* native recurring/series appointments plus a "linked appointments" field; cancelling one prompts "cancel the others?". Ninety percent of the value, no reservation semantics.
2. *The checkout convention:* the strongest sequencing tool in a small office is booking the next visit before the patient leaves the building. A checkout prompt with the recommended interval pre-filled beats any pathway engine, and it is a UI affordance rather than a subsystem.
3. *Sequence on the recall side, not the calendar side:* define "post-op ladder: +2 weeks, +6 weeks, +6 months" as recall dates rather than reserved slots. No capacity is stranded, the ladder survives a delay, and it feeds the vacancy-recovery engine (B2) rather than competing with it.

**Reframe.** Not "reserve a linked sequence of slots" but: **make the next visit impossible to forget without making today's capacity impossible to sell.**

**Gaps it leaves.** What happens when step one is cancelled after step two's authorization was already obtained. Capacity stranded by unclaimed holds and who reclaims it. Multi-provider pathways where one provider's template changes. Pathway definition changes while instances are in flight. Patient-visible representation of a sequence — the patient needs to know the plan more than the calendar does.

## B6. Access observability

**Verdict: differentiator**, cheap, and unusually trust-building for an open-source product — but the beta form should be five numbers, not a semantic metrics platform.

**Make it more useful.** One weekly **Access one-pager** emailed to the practice manager: fill rate, third-next-available per provider, no-show percentage, same-day cancellations recovered, and top constraint-rejection reasons. That last one is the sleeper metric and is the strongest reason to keep this feat: rejection reasons tell the practice precisely what patients asked for and could not get, which is the only input to a rational capacity decision. Ship trend lines, not definitions.

**Alternate/better solutions.**

1. *Simpler mechanism:* emit an immutable `Access Event` stream and ship five saved Frappe reports over it. Versioned metric semantics is a later-cluster concern; in beta the practice needs direction, not citability.
2. *Log demand, not just supply:* record every *search* — what was asked for and what was offered — rather than only what was booked. Unmet demand is the number that changes hiring and hours decisions, and no incumbent surfaces it.
3. *Benchmark against self:* thirteen-week trailing comparison per provider, no denominators debate, no external benchmark. A small practice needs to know whether it is getting better, not where it sits in a percentile.

**Reframe.** Not "publish access metrics" but: **make visible the appointments patients wanted and could not have — capacity decisions are made from unmet demand, not from fill rate.**

**Gaps it leaves.** Third-next-available is meaningless in aggregate and must be per provider *and* per appointment type; the doc does not say so. Practice-initiated cancellations polluting no-show and fill numbers. Template changes invalidating historical fill rate comparisons. Seasonality with only one year of data. Telephone metrics — hold time, abandoned calls, time to book — which are the front office's actual bottleneck and are absent from every feat in this cluster.

---

# 3. Patient Engagement And Communications

## C1. Unified operational threads

**Verdict: essential-core.** The single largest daily friction in a small front office is that texts, voicemails, portal messages, and sticky notes live in four places and nothing has a "done" state. One thread per patient with an owner and a disposition is, for a receptionist, most of the product.

**Make it more useful.** Thread by **patient**, never by channel or campaign. Every inbound item gets an owner and one of three states (new / mine / done) reachable by keyboard. Render the actionable context inline — next appointment, balance, previsit status — so a reply can be handled without navigating away. Default ownership to whoever is on desk today. The design target is explicit and testable: **the inbox must be emptiable by 5pm by one person.**

**Alternate/better solutions.**

1. *Simpler mechanism:* every message is a comment on the patient record, and "unresolved" is one checkbox. The thread is the patient timeline, which already exists in Frappe. No conversation object at all.
2. *Route by intent rather than by patient:* classify inbound replies with keyword rules — cancel, reschedule, running late, billing question, refill — and drop each into the domain queue that can act on it. The receptionist never reads the eighty percent that are confirmations and cancellations, and the schedule updates itself.
3. *Voice-last:* leave telephony out of beta entirely; log call outcomes as two-click dispositions attached to the thread. Building voice threading for a practice with one phone line buys almost nothing and costs a great deal.

**Reframe.** Not "unify channels into threads" but: **no patient communication may end in an unowned state — and the pile must be provably emptiable in a day by one person.**

**Gaps it leaves.** Two patients sharing one phone number — the household case, which is extremely common and which patient-keyed threading gets structurally wrong. Inbound photos (MMS of an insurance card) with no destination. Urgent clinical content arriving in an administrative inbox — a genuine patient-safety exposure that this feat creates and does not mitigate (see F5). Staff replying from personal phones. After-hours inbound with no coverage model. Replies to a no-reply email address. Message retention as PHI. Nothing describes what a *closed* thread means when the underlying task is still open.

## C2. Cadence orchestration

**Verdict: essential-core**, on the strict condition that the beta ships opinionated defaults rather than an orchestration builder. A cadence designer is a feature for a marketing team; a small practice needs three cadences that work correctly on day one.

**Make it more useful.** Ship exactly three named cadences — **appointment**, **previsit**, **balance** — each rendered as a visible timeline diagram with about four adjustable knobs. Two behaviors matter more than the schedule itself: **suppress on response** (a confirmed appointment silences the rest of its cadence) and **cross-cadence suppression** so a patient never receives three messages in one day. Add a practice-wide daily send cap with an obvious override. Idempotency keyed on `(person, purpose, appointment, offset)` so a scheduler retry cannot double-send.

**Alternate/better solutions.**

1. *Simpler mechanism:* an `event → template → offset` table plus the Frappe scheduler. No state machine, no orchestration engine, and the whole configuration is legible in one screen.
2. *Digest instead of per-purpose sends:* one daily "your practice update" message bundling everything currently due for that patient. Fewer messages, higher open rates, lower carrier-filtering risk, cheaper, and structurally incapable of the three-messages-in-an-hour failure.
3. *Let the patient choose the cadence:* a one-tap preference at first contact — "remind me: a lot / once / not at all" — outperforms any heuristic the practice can author, and it converts a configuration problem into a consent artifact the practice already wants.

**Reframe.** Not "orchestrate message cadences" but: **each patient should receive the fewest messages that still produce the behavior — and any response should immediately stop everything it answered.**

**Gaps it leaves.** Rescheduled appointments leaving stale sends queued against the old time — the most common and most embarrassing failure of every reminder system. Time zones and daylight-saving transitions. Practice holidays and unplanned closures. Messages queued before a patient merge. The reverse cadence: a snow day or provider illness requiring twenty-two outbound cancellations in ten minutes, which no described cadence handles. Patients with multiple appointments the same day. Deceased patients still on a cadence.

## C3. Consent decision service

**Verdict: essential-core as a mechanism, differentiator in how it is expressed.** TCPA exposure is real even for a two-provider practice, and "no bypass hidden in campaign tooling" is exactly the right architectural stance — it is the difference between a consent feature and a consent guarantee. It must, however, be completely invisible in daily use.

**Make it more useful.** Make it one library call every send path must invoke, returning `allow|deny + reason + evidence_id`, and enforce that architecturally: a test that fails if any send path reaches a provider adapter without a decision record. Capture consent once at registration with purpose-scoped checkboxes on the signed financial/communications acknowledgment. Honor STOP instantly across every purpose on that address, and give staff a visible, one-click way to record a verbal opt-out at the desk, because that is how most opt-outs actually arrive.

**Alternate/better solutions.**

1. *Simpler mechanism:* a `Contact Permission` table keyed `(person, channel, purpose)` with sensible seeded defaults — deny-by-default for marketing, allow-by-default for transactional messages within an existing treatment relationship. One function, no service, no jurisdiction engine in beta.
2. *Key consent to the address, not the person:* the phone number is the consent-bearing object. This is the only model that gets shared household phones and reassigned numbers right, and person-keyed consent gets both wrong in ways that produce exactly the complaints that generate liability.
3. *Detect rather than gate:* stamp every send with its permission basis and run a nightly job that flags any send lacking one. Far cheaper than a gating service, and for a single-practice beta the enforcement gap is a day rather than a lawsuit — though this trades safety for cost and should be a conscious choice.

**Reframe.** Not "evaluate consent before every send" but: **it must be structurally impossible for a message to leave this system without a recorded reason it was allowed to.**

**Gaps it leaves.** Reassigned phone numbers, the most common real-world TCPA trap, which no consent record can detect without a reassignment database. Verbal consent given at the desk and its evidence. Consent captured by the connected EHR's portal and not synchronized. A proxy consenting on a patient's behalf. Legally required notices (a closure, a breach, a records request) that must override a marketing opt-out, and the representation of that legal basis. Consent expiry and re-consent triggers. Which jurisdiction's rules apply when the patient moves.

## C4. Cohort campaigns with guardrails

**Verdict: valuable-enhancer**, and questionable as an audience-building tool. A small practice sends perhaps six campaigns a year: flu shots, a closure, a departing provider, a recall sweep, a new service, a payer-network change. That does not justify audience tooling; it justifies a safe blast.

**Make it more useful.** Four prebuilt audiences — overdue recall, next week's appointments, balances over N days, all active patients — plus an exact recipient count, a mandatory test-send to the sender, and a ten-minute undo window. Drop the approval workflow: in a three-person office the sender *is* the approver, and a second-approver requirement will be satisfied by clicking twice. Replace approval with **reversibility and preview**, which are the controls that actually prevent harm at this scale.

**Alternate/better solutions.**

1. *Simpler mechanism:* a saved report plus a "message these people" button. The audience is a report, not a campaign object, and the practice can already read reports.
2. *Trickle instead of blast — the important one:* convert a campaign into per-patient work items metered across days, so 400 flu-shot messages become 40 a day for ten days. A blast creates an inbound callback surge that a two-person desk physically cannot absorb, which is why small practices stop sending campaigns after the first one. Metering is the feature.
3. *Campaigns produce recalls, not messages:* a campaign creates recall tasks that the ordinary outreach machinery works, so the campaign's outcome is measured in appointments rather than delivery receipts, and it inherits every consent and cadence protection automatically.

**Reframe.** Not "send to a cohort safely" but: **generate demand at a rate the front desk can actually answer.**

**Gaps it leaves.** The reply storm, which is the real failure mode and is never mentioned. Excluding deceased, moved, merged, and already-scheduled patients. Unsubscribe scope — this campaign versus all outreach. Content review for messages that could read as clinical advice. Language selection per recipient. Whether campaign sends to patients with a balance are debt collection communications, which carry a separate legal regime.

## C5. Digital task delivery

**Verdict: essential-core.** Secure links are the load-bearing mechanism of the entire cluster — they are how paperwork, payment, confirmation, and rescheduling reach the patient without a portal account, an app, or paper. Everything in sections 4 and 5 either rides this or is redundant with it.

**Make it more useful.** Ship **one universal link per appointment** — "your visit page" — that always renders everything currently due, rather than separate links per task. One long-lived signed token per appointment, no account, no password; a date-of-birth step-up only when the page discloses a balance or accepts money. Regenerate on reschedule and invalidate the old one. Give staff a "view exactly what the patient sees" control and a one-click resend, because half of all support interactions are "I can't find the link."

**Alternate/better solutions.**

1. *Simpler mechanism:* one signed URL per appointment, expiring at visit + 7 days, rendering a checklist. This is the highest-converting pattern in the industry precisely because it has no login, and it eliminates the entire account-recovery support burden that a three-person office cannot carry.
2. *Kiosk parity for free via QR:* the same page opens by scanning a lobby poster. "Mobile" and "in-office" become one implementation, one test surface, one set of bugs — and channel parity stops being a feature and becomes an accident of the architecture.
3. *Reply-to-act for single-step tasks:* confirm, cancel, request an interpreter, ask for a call back. No page, no link, no data plan, works on a flip phone.

**Reframe.** Not "deliver tasks by secure link" but: **there is exactly one place a patient goes for a visit, it needs no account, and it is always correct about what is left to do.**

**Gaps it leaves.** Link forwarding and household members opening each other's links — an unavoidable disclosure risk with no described mitigation beyond the DOB step-up. Carrier and spam filtering of link-bearing SMS, which is a live operational threat. Expired links and self-service re-request. Patients without data service. Screen-reader accessibility. A link opened after the visit, where the correct behavior is undefined. Attribution: proving *who* actually completed and signed, when possession of a link is the only credential — which matters legally for consents.

## C6. Communication accountability

**Verdict: valuable-enhancer**, containing one essential-core obligation: **failures must become work.** An attempt ledger nobody reads is an audit artifact; an undeliverable phone number is a task with a due date.

**Make it more useful.** Collapse the whole feat into a single **"patients we can't reach"** queue — hard bounces, invalid numbers, carrier blocks, and repeated non-response — each with a next action (call the alternate number, ask at the next visit, mail it). Surface reachability as a badge on the patient record and as a check-in prompt, because the only reliable moment to fix a bad phone number is when the person is standing at the desk.

**Alternate/better solutions.**

1. *Simpler mechanism:* one `Contact Attempt` row per send with a delivery status, and one report. That is the accountability; the ledger is a table, not a subsystem.
2. *Reachability as a maintained score:* delivery outcomes update a per-address health value; anything below threshold raises a "verify contact info" flag that fires at check-in. This closes the loop at the only point where correction is free.
3. *Delegate to the vendor:* consume provider delivery webhooks and materialize only the exceptions. Do not rebuild a delivery ledger that Twilio-class vendors already keep better.

**Reframe.** Not "log every communication attempt" but: **maintain, at all times, a short list of the patients we currently cannot reach — and fix it while they are standing in front of us.**

**Gaps it leaves.** 10DLC / A2P registration and carrier filtering, which is a genuine go-live blocker for a small practice sending healthcare SMS and appears nowhere in the packet. Email domain reputation and SPF/DKIM setup during implementation. "Delivered" versus "read" versus "understood." Message-content retention as PHI and its schedule. Producing proof-of-notice for a billing or no-show dispute. Attempts made by phone (the dominant channel) never entering the ledger at all, which makes every accountability number wrong.

---

# 4. HealthLinc App Onboarding

**Cluster note.** The small-practice beta overview already reclassifies the companion app as a layered later capability, and this review agrees emphatically. A native app is a distribution channel, a store-review cadence, a support surface, and a permission model — four costs a three-person practice pays and zero of which the practice's patients asked for. Every feat below is therefore reviewed twice: as written (app-dependent), and as the web-and-SMS equivalent that delivers the same outcome in beta. The *principles* buried in this document are excellent and should be extracted; the app should not be built to hold them.

## D1. Appointment-aware adoption campaigns

**Verdict: defer.** Store routing, install attribution, and campaign-context preservation are app-economy machinery in service of an install, and an install is not an outcome. The outcome — the patient completes paperwork before arrival — is fully served by a no-login link that works on every device the practice's patients already own. Asking a 71-year-old with a Tuesday appointment to install an app first is a conversion cliff, not a funnel.

**Make it more useful.** If an app ever exists, never invite cold. Invite at the moment of demonstrated value: the patient finishes their previsit on the web, and *then* sees "next time, do this in one tap." Install becomes an optimization of a flow that already works rather than a prerequisite for one that does not.

**Alternate/better solutions.**

1. *Simpler mechanism:* no app. The appointment link (C5) is the product, and its conversion rate will exceed any install funnel by a wide margin.
2. *Wallet pass instead of an app:* an Apple/Google Wallet appointment pass gives the patient date, time, address, directions, a check-in QR, and lock-screen reminders — push-adjacent behavior with zero app build, zero install friction, zero store review, and zero permission dialogs. This is a genuinely better mechanism for most of what the app is proposed to do.
3. *Installable web app:* offer "add to home screen" after a second completed task. Same icon on the same home screen, one codebase, no store.

**Reframe.** Not "convert appointment reminders into app installs" but: **the patient should never install anything to finish their paperwork — and whatever they use should still be there next year.**

**Gaps it leaves.** Store review latency versus practice release cadence — a form change should not require an app release. Patients who install and never link. Multi-practice patients holding six healthcare apps. Deprecation: what happens to the patients on an app the project stops maintaining. And the support burden: the practice's own staff must troubleshoot an app they did not build.

## D2. Identity-assured account linking

**Verdict: valuable-enhancer**, wrapping an **essential-core principle**: possession of a phone is not authority over a record. That principle must hold in beta whether or not an app exists, because the link-based flow has exactly the same exposure.

**Make it more useful.** Bind the credential to the *appointment*, not to the person, and require the step-up (date of birth, or a code sent to a second channel) only when the page discloses data or writes financial state. Rate-limit, lock after three failures, and give staff an explicit unlock action that records who unlocked and why. Make staff-assisted linking generate evidence automatically rather than relying on staff to document it.

**Alternate/better solutions.**

1. *Simpler mechanism — no accounts at all in beta:* signed per-appointment links plus a DOB step-up for money. Accounts generate password-reset calls, and every one of those calls lands on the same two people who are checking in patients. Deleting the account concept removes an entire support category.
2. *Proof in person:* staff hand the patient a code at checkout or scan a QR on the patient's phone. In-person proofing at an ambulatory practice is the strongest and cheapest identity assurance available and is completely unused in the proposal.
3. *Payer-anchored proofing:* matching an active 271's member ID plus DOB is stronger evidence than name-plus-DOB and the practice already holds it. It also succeeds for patients whose demographics are stale, which is the exact failure case the doc worries about.

**Reframe.** Not "verify identity before granting an account" but: **each capability states the proof it requires, and low-stakes tasks must never inherit high-stakes friction.**

**Gaps it leaves.** Patients with no email. One email shared across a household. The fact that date of birth is not a secret and is the weakest possible second factor. Account recovery. Patients who moved and now fail address matching. Staff-assisted linking becoming the default path by habit, silently voiding the assurance model. Revoking access when a proxy relationship ends. Nothing describes what a *failed* verification does for the patient — the dead-end the equity commitment forbids.

## D3. Task-specific activation

**Verdict: defer** as app deep-linking; the underlying rule — **every message lands on the one action it is about** — is essential-core and belongs in C5, not here.

**Make it more useful.** Make it a house rule rather than a feature: every outbound message carries at most one call to action, and lands on a page where that action is the only prominent control. Re-evaluate the task server-side at open time — it may already be done, superseded, cancelled, or now owned by staff — and say so plainly rather than presenting a stale form.

**Alternate/better solutions.**

1. *Simpler mechanism:* there is one destination (the visit page) and it self-orders by urgency. Zero deep links to maintain, and no combinatorial matrix of message type × destination.
2. *Reply-to-act* for anything single-step.
3. *Continuation tokens:* the link encodes "resume at step 3," which makes cross-device resume a property of the URL rather than a synchronization feature.

**Reframe.** Not "deep-link the user to the pending task" but: **the patient should never have to find anything — one destination, always correct about what is left.**

**Gaps it leaves.** Task completed by staff or a family member in the interim. Task invalidated by a reschedule mid-flow. A link opened by the wrong household member. Session expiry losing entered data. Back-button and refresh behavior on a multi-step form. What the page shows when *nothing* is pending, which is most opens.

## D4. Progressive permission sequencing

**Verdict: defer.** This is competent UX craft in service of two deferred things (an app, and sensors). It has zero beta value because the beta asks for no device permissions at all — which is itself the strongest argument for the web-first path.

**Make it more useful.** Extract the principle and apply it to the consents the beta *does* need — SMS, email, card-on-file, autopay: ask at the moment of demonstrated benefit, state the fallback, state how to revoke, and never ask twice after a refusal. Encode it as a product-wide rule for consent prompts.

**Alternate/better solutions.**

1. *Wallet passes and web push* cover notification value with no permission ladder.
2. *Do not create the problem:* SMS reach is effectively universal; permission sequencing is a cost incurred by choosing an app, not a cost of solving the patient's problem.
3. *Ask at the desk:* a verbal ask with a human explaining the benefit converts better than any in-app prompt, and refusal is free.

**Reframe.** Not "sequence permission requests" but: **never ask for a capability before the patient has felt what it does — and design so refusal costs them nothing.**

**Gaps it leaves.** OS-level revocation later, which silently degrades a feature the practice believes is working. iOS's effectively one-shot prompts. Notification fatigue across the patient's other healthcare apps. What breaks when permission is absent — the fallback is asserted but never specified.

## D5. Governed family profiles

**Verdict: valuable-enhancer**, over an **essential-core need**. A parent managing three children plus an aging parent is the ordinary case in ambulatory practice, not an edge case, and it is the case the entire cluster models worst. The profile-switcher is app-specific; the authority model is not.

**Make it more useful.** Put the subject in the *link*: one link per patient per appointment, with the subject's name and date of birth pinned in the page header at all times and restated immediately above any signature. Where a family genuinely shares a visit block, render a **household visit page** listing each member and their remaining tasks. Derive what a proxy may see entirely from the effective-dated relationship table (A4) so there is no second authority model to keep in sync.

**Alternate/better solutions.**

1. *Simpler mechanism:* the household page. No profile switching, no session subject, no "who am I acting as" state to get wrong — the list is the model.
2. *Authority-derived rendering:* the surface has no proxy concept at all; it renders exactly what A4 says this person may act on today. One source of truth, and expirations are automatic.
3. *Paper parity:* a single family check-in sheet at the desk is a legitimate and instructive fallback — designing it first reveals the minimum data model, and it is what happens when the phone is dead.

**Reframe.** Not "let one device switch among profiles" but: **a household is the unit that shows up, signs, pays, and gets reminded — model the household and the profile problem disappears.**

**Gaps it leaves.** The serious one: a teenager's confidential services surfacing on a parent's page. Proxy authority expiring at the age of majority mid-session. Divorced parents both holding access with different financial responsibility. A proxy who should see scheduling but not balances — authority is not binary. Guardianship of an adult, its documentary evidence, and its renewal. A caregiver who is not a legal representative but does all the scheduling, which is extremely common and has no model here.

## D6. Exportable adoption funnel

**Verdict: questionable.** It measures adoption of a channel rather than achievement of an outcome, and it will therefore reward the wrong thing: a practice can have a beautiful install funnel and still have every patient doing paperwork at the desk. It also creates an attribution and tracking surface the packet elsewhere works hard to avoid.

**Make it more useful.** Measure the outcome funnel instead: percentage of appointments with previsit complete at T-24h, percentage of patients who arrive with nothing to do at the desk, and average desk time per arrival. Channel is a *dimension* on those numbers, not the subject of measurement — which also makes the app's value falsifiable.

**Alternate/better solutions.**

1. *One number:* "share of visits that required no desk paperwork." Everything else is diagnostic detail.
2. *Explicit kill criterion:* compare completion rates by channel and pre-commit to retiring the app if it does not beat the plain link by a stated margin. That converts a metrics feature into a decision.
3. *Instrument the desk:* time arrival-to-roomed automatically. Staff-side time saved is the metric the practice will actually pay for.

**Reframe.** Not "measure the install-to-value funnel" but: **measure how many patients walked in with nothing left to do — the channel that produced it is a detail.**

**Gaps it leaves.** Patients who complete their previsit by phone with staff help would appear as funnel failures despite being successes. Practices with high walk-in volume have no denominator. Attribution identifiers as a re-identification surface. No accounting for effort: a funnel stage that requires three staff calls is not the same as one that requires none.

---

# 5. Mobile Registration And Previsit Completion

**Cluster note.** Reading this document as *the responsive visit page* rather than *the app* makes it the strongest atomic in the cluster. Almost everything here is beta-essential once the delivery channel is decoupled from the capability.

## E1. Appointment-shaped previsit plans

**Verdict: essential-core.** This is the organizing object of the entire front-office funnel: an appointment-scoped task list with visible ownership, consumed identically by the patient page, the kiosk, and the staff worklist. Without it, every other feat here is a loose form.

**Make it more useful.** Do not author plans per appointment type. **Generate** the plan from readiness blockers (A6), form validity windows (E4), and coverage staleness — so it self-maintains and cannot drift from what the practice actually needs. Cap the visible list at five items; beyond five, patients abandon. Show remaining *time* ("about 2 minutes left"), not percentage complete — patients respond to cost, not progress. Order by what blocks the visit, not by form order.

**Alternate/better solutions.**

1. *Simpler mechanism:* there is no plan object. The task list is a computed view over "required minus known," recomputed on every open. Nothing to version, nothing to invalidate, nothing to migrate when an appointment type changes.
2. *Progressive capture across visits:* ask for one additional thing per visit rather than everything before the first. Long-run data completeness improves and the first-visit wall disappears — which is the actual reason patients abandon intake.
3. *One list, two renderings:* the staff worklist and the patient checklist are literally the same computed object. This structurally eliminates the "the desk asked me for something the app said I finished" failure.

**Reframe.** Not "assemble a previsit task plan" but: **the difference between what this visit needs and what we already know is the only list — render it to whoever can close it fastest.**

**Gaps it leaves.** Same-day and next-morning appointments where there is no previsit window. Task dependencies (coverage must be confirmed before an estimate can exist). The EHR being unavailable when clinical instruments are due. Tasks that expire mid-window (an annual consent rolling over between issue and visit). Appointment type changed after the plan was issued. A patient with two appointments the same day generating duplicate tasks. And walk-ins, who have no plan at all.

## E2. Guided registration confirmation

**Verdict: essential-core.** For the large majority of visits — established patients — "is this still right?" is a fundamentally different and far better interaction than "fill this in," and it is the single biggest reduction in patient effort available anywhere in this cluster.

**Make it more useful.** One screen, everything visible, inline edit, and a single **"Yes, all correct"** that stamps a verification date across every block. Route only *changed* fields to staff review; unchanged confirmations apply automatically and generate no work. Distinguish the fields where a patient assertion is authoritative (phone, email, mailing address) from those where it is a claim to verify (insurance, legal name), and only queue the second kind.

**Alternate/better solutions.**

1. *Simpler mechanism — verification dates:* every demographic block carries `last_verified_on`; the previsit asks only about blocks older than N days. No diff engine, no change-review queue, and staleness becomes a first-class, reportable property.
2. *Confirm by text:* "Reply Y if your address and insurance are unchanged." One message, no page, covers most established patients, and works on any phone. The patients who reply N become the call list.
3. *Desk-side diff at check-in* with a QR the patient scans to correct anything wrong on their own phone — the same page, zero new implementation, and it catches the patients who ignore pre-visit messages entirely.

**Reframe.** Not "guide the patient through confirming their record" but: **prove what we already know is still true, and only ever ask about what has changed or gone stale.**

**Gaps it leaves.** A patient asserting a change that must be verified before it can be used on a claim. Confirmation fatigue — the patient who taps "all correct" without reading, which is the dominant behavior and undermines the whole evidentiary value. Changes that invalidate an existing estimate or eligibility result and should trigger a re-run. Conflicts between what the patient says and what the payer says. A coverage change discovered at confirmation time three hours before the visit, with no described urgent path.

## E3. Assisted insurance capture

**Verdict: valuable-enhancer.** Patient-side capture is strictly better than desk-side capture because it happens before arrival, when there is still time to fix a problem. The OCR remains the weak component (see A3), and the confirmation UX risks turning the patient into an unpaid proofreader of a bad guess.

**Make it more useful.** Convert capture into an *answer*: photo or typed entry → immediate eligibility check → show the patient the result in plain language ("Found it — active coverage, $30 copay for this visit"). That result is why a patient will bother. If eligibility fails, do not nag the patient; create a staff task, because the failure is usually not something the patient can fix.

**Alternate/better solutions.**

1. *Simpler mechanism:* searchable payer list plus typed member ID, verified by 270/271. Photo optional, stored as evidence only.
2. *Coverage discovery by demographics:* query the clearinghouse's discovery service and ask the patient only to *confirm* which of the returned plans is theirs. No typing, no photo, and it finds the coverage patients forget they have.
3. *"Same as last time":* one tap, re-verified silently in the background, with the patient contacted only if it fails. For the majority of returning patients this is the entire interaction.

**Reframe.** Not "capture the insurance card" but: **tell the patient what their visit will cost and confirm their coverage is live — the card is one of several ways to get there, and the worst one.**

**Gaps it leaves.** A patient photographing a spouse's card, which is the correct card but the wrong subscriber. Plans effective in the future. Monthly Medicaid plan churn. Secondary coverage patients routinely forget. Auto and workers' compensation with no card and an entirely different party model. Uninsured patients, who need the self-pay and Good Faith Estimate lane the beta overview flags as missing and this feat does not provide.

## E4. Governed forms and signatures

**Verdict: essential-core.** The signed financial policy, Notice of Privacy Practices acknowledgment, assignment of benefits, and consent to treat are the practice's legal spine, and they are the thing small practices handle worst — a folder of paper nobody can find when a payer or an auditor asks. Version, signer, timestamp, and an immutable rendering are the whole requirement.

**Make it more useful.** Define validity rules so a returning patient signs *nothing* unless a document version changed or a validity period lapsed — annual for the NPP acknowledgment, until-changed for financial policy, per-visit for consent to treat. Store a rendered PDF snapshot of exactly what was displayed plus a content hash, not a re-render from a template that may have changed. Signature = typed name + timestamp + IP/device + document hash; anything more is theater at this scale.

**Alternate/better solutions.**

1. *Simpler mechanism — evidence-first:* ship four canonical documents under version control with an acknowledgment record; skip form-builder ambitions entirely in beta. Practices need their four documents to be airtight far more than they need to author a fifth.
2. *One welcome bundle:* combine NPP, financial policy, assignment of benefits, and communications consent into a single acknowledgment with itemized disclosure. One signature instead of four, one evidence record, dramatically higher completion.
3. *Desk tablet parity:* the same document, version, and evidence machinery driven from a staff device. Channel parity is then free rather than a separate kiosk feature.

**Reframe.** Not "deliver and sign forms digitally" but: **at any moment, produce the exact document this person agreed to, on this date, in this version — and never ask them to sign it twice.**

**Gaps it leaves.** Who may sign for a minor or an incapacitated adult and what evidence of that authority is retained — the highest-risk gap here. Language versions and which one is legally authoritative. A document version changing between signature and visit. Revocation of a previously given consent. Retention and destruction schedules. Producing a complete signed set for an audit, a payer request, or a subpoena. Witness requirements where a state imposes them.

## E5. Resumable financial preparation

**Verdict: differentiator.** Pre-service collection is the single highest-yield revenue behavior available to an ambulatory practice, and card-on-file with a recorded authorization is transformative for patient accounts receivable — a small practice's worst-performing asset. This is where the front office directly moves money, and the constitution's human-authority constraints make openPractice's version *more* trustworthy than incumbents', not less.

**Make it more useful.** Make the ask concrete and small: "your copay is $30 — pay now, or save a card and we'll charge only what insurance leaves." Fold the card-on-file authorization language into the financial policy signature so it is one act rather than a second negotiation. Label the estimate an estimate in the patient's words, state the basis, and provide the dispute path (and, for self-pay, the Good Faith Estimate the beta overview flags).

**Alternate/better solutions.**

1. *Simpler mechanism — copay-only prepay:* collect the plan's stated copay straight from the 271. Near-perfect accuracy, almost no effort, and it covers the majority of ambulatory visits without any estimation engine at all.
2. *Card on file with a capped authorization* ("up to $200 of post-insurance balance") — this is the actual revenue mechanism in modern practice management, and the packet's human-authority principles can be honored by capping, notifying before charge, and making every charge reversible, rather than by prohibiting it.
3. *Terms before the shock:* for anything over a threshold, lead with a payment plan rather than a balance. A patient offered $50/month keeps the appointment; a patient shown $600 cancels it, which converts a collections problem into an access problem.

**Reframe.** Not "prepare the patient financially before the visit" but: **the patient should know what they owe and have already chosen how to pay it before they walk in — and the practice should never have to mail a statement for a copay.**

**Gaps it leaves.** Estimates that prove wrong after coding, and the credit/refund control that must exist to unwind them. Deductible-season swings that make January estimates systematically wrong. Patients who prepay and then cancel. HSA/FSA cards and their decline behavior. Collecting from a guarantor who is not present and did not see the estimate. What the practice does when the patient declines to pay — the answer must be "see the patient," and the system must not imply otherwise. Good Faith Estimate obligations and the dispute-evidence trail for self-pay patients.

## E6. Readiness-led arrival

**Verdict: essential-core**, with the completion percentage as the weak component. This is the data source for the front-desk cockpit and the join between the patient's work and the staff's work.

**Make it more useful.** Replace the percentage with three states per appointment — **Ready / Needs one thing / Needs the desk** — plus the named blocker. Sort the day view by "needs the desk," because that is the queue that will consume staff time. Make every blocker one-click resolvable *by staff on the patient's behalf*, since a receptionist can fix in fifteen seconds what a patient will not fix at all. The percentage is a metric; the state is a decision.

**Alternate/better solutions.**

1. *Simpler mechanism:* a `blockers[]` array on the appointment and a count badge. No weighting math, and no need to ever settle the "how do we weight a blocking identity defect against an optional task" question the doc raises.
2. *Time-to-clear instead of completeness:* "this patient will take about four minutes at the desk." Directly actionable for deciding whom to call ahead of time, and it aggregates meaningfully into "tomorrow morning needs eleven desk-minutes."
3. *Route the blocker to whoever can clear it* — coverage to the biller, forms to the patient, identity to the desk. If the work is already routed, the appointment does not need a score.

**Reframe.** Not "publish previsit completion to staff surfaces" but: **the day view should be sorted by how much desk time each arrival is about to consume.**

**Gaps it leaves.** Walk-ins, who have no plan and therefore no readiness. Readiness that changes after arrival (the patient produces a new card). Blockers only a provider can clear. The patient who completed everything online and still queues behind the desk because nothing tells staff to wave them through — a seam that destroys the entire value proposition and is not addressed anywhere. How readiness interacts with the clinical reality that the visit proceeds regardless.

---

# 6. AI Intake Assistance

**Cluster note.** The beta overview's sequencing — deterministic workflows, consent, corrections, evidence, metrics, and evaluation data *before* AI — is correct and this review applies it strictly. Several feats here have deterministic twins that are beta-essential and are being smuggled in under an AI framing; those are separated out and promoted. The AI-specific machinery is deferred without prejudice.

## F1. Question-by-question interpreter

**Verdict: defer** as generated explanation; the deterministic twin (authored "why we ask" text per field) is a **valuable-enhancer** available immediately and is a precondition for evaluating the AI version at all.

**Make it more useful.** Make "why we ask this" a required schema property on every patient-facing field, rendered inline rather than behind a tooltip. Then AI, later, handles only the phrasings the authored text did not anticipate — a much smaller and much more evaluable problem.

**Alternate/better solutions.**

1. *Simpler mechanism — write better questions.* Most intake confusion is bad wording inherited from a paper form. A one-day plain-language audit of forty fields returns more comprehension than any explanation layer, permanently, at zero runtime cost and zero PHI exposure.
2. *State the consequence, not the definition:* "we need this to bill your insurance — without it you may get the whole bill" outperforms a definition of "subscriber."
3. *Instrument confusion:* log per-field abandonment and dwell time, fix the worst five each quarter. Data-driven, no model, and it produces the evaluation dataset the AI version would eventually need.

**Reframe.** Not "explain each question on demand" but: **a question that needs explaining is a question written badly — measure which ones, and rewrite them.**

**Gaps it leaves.** Explanations drifting from legally authored consent text — a real liability where the explanation contradicts the document. Translation of explanations, and which version governs. Explanation of clinically authored questions, which crosses the ownership boundary. Help text going stale after a form version changes, with no linkage forcing a review.

## F2. Confirmable answer drafts

**Verdict: essential-core**, once stripped of the AI framing. Prefilling from the record with explicit confirmation is a database join, not a model, and it is table stakes. The genuinely good idea here is the governance: **a suggested value stays visibly pending until a human accepts it.** Keep that; drop the copilot.

**Make it more useful.** Prefill everything the system knows, show the source and the verification date beside each carried-forward value, and offer "confirm all" with per-field edit. Store provenance as a field-level attribute (patient-entered / carried-forward / staff-entered / payer-derived) so downstream disputes can be answered without an AI ledger.

**Alternate/better solutions.**

1. *Simpler mechanism:* prefill plus a provenance flag. That is the entire feature.
2. *Ask only for deltas* (E2) — if nothing is stale, nothing is asked, and the confirmation problem disappears.
3. *Recency convention:* values verified within 90 days auto-confirm; older values require an explicit touch. One rule replaces per-field policy.

**Reframe.** Not "draft answers for the patient to confirm" but: **show what we believe, say where we got it and when — and make disagreeing easier than agreeing.**

**Gaps it leaves.** Stale errors perpetuated indefinitely by confirm-all, which is the dominant behavior. Fields where carrying forward is actively wrong — employment, emergency contact after a divorce, anything clinical. Provenance surviving a merge. Patients confirming things they cannot actually judge (a group number). No mechanism for a patient to say "I don't know."

## F3. Pre-submission completeness coach

**Verdict: valuable-enhancer**; the deterministic half is essential-core, the bounded-AI half defers.

**Make it more useful.** Inline field validation plus a "3 things left" bar. The cross-field checks worth building are few and specific: date of birth versus stated guarantor relationship, coverage effective dates versus appointment date, "relationship to subscriber = self" while subscriber name differs from patient name, patient state versus the payer's plan geography. Five real checks beat a general coaching layer.

**Alternate/better solutions.**

1. *Simpler mechanism:* validate the intake against the claim dry-run (A6, alternative 1). One rule source for the entire product, guaranteed consistent with what actually fails downstream.
2. *Prevent by construction:* bound the date pickers by plan effective dates; make the conflicting entry unrepresentable rather than detectable.
3. *Coach the staff instead:* surface the conflicts at check-in with the patient physically present. The cheapest possible resolution point, and it needs no patient-facing surface at all.

**Reframe.** Not "coach the patient toward completeness" but: **make the wrong answer impossible to enter, and catch the rest while the patient is still in the building.**

**Gaps it leaves.** Conflicts introduced *after* submission. Apparent conflicts that are actually true (a 19-year-old who is genuinely their own subscriber) and the alert fatigue that follows. Non-blocking warnings that are ignored permanently, with no escalation. Accessible presentation of errors for screen readers.

## F4. Cited administrative concierge

**Verdict: defer**, and questionable at this scale even later. The knowledge governance the doc correctly demands — publishing, approval, effective dates, localization, expiry, fail-closed behavior — is more work than the answers are worth for one practice, and a single well-built static page answers the same 95% of questions with no corpus to maintain and no liability surface.

**Make it more useful.** If ever built, restrict it to a fixed FAQ set with exact-match retrieval, fail closed to "I'll have the office call you" with a work item, and instrument deflected calls so the feature can be killed on evidence.

**Alternate/better solutions.**

1. *Simpler mechanism — a genuinely good "Your visit" page:* address with a map, a photo of the parking entrance and the door, what to bring, when to arrive, the cancellation policy, and accepted plans. Attached to every appointment message. This answers almost every real question and costs one afternoon.
2. *Answer from the record, not from prose:* most questions are "when, where, how much, with whom" — those are fields on the appointment, not knowledge-base entries, and a field-driven answer cannot hallucinate.
3. *A 30-entry curated FAQ with search plus a "call us" fallback* has better precision than retrieval over practice prose, and the practice can edit it.

**Reframe.** Not "answer administrative questions conversationally" but: **publish the answers to the twenty questions patients actually ask, on the page they are already looking at.**

**Gaps it leaves.** Questions that are really financial advice — "will my insurance cover this?" is unanswerable and dangerous to answer. Stale hours during an unplanned closure, where a wrong answer sends a patient to a locked door. Language coverage of the corpus. The practice never maintaining the corpus, which is the realistic outcome. Liability for an incorrect "yes, we accept your plan."

## F5. Clinical-boundary router

**Verdict: essential-core**, reframed. As written it is an AI feature and would defer with the AI; but the beta *already* creates the hazard it addresses the moment two-way SMS exists (C1). "I've had chest pain since last night" arriving in an administrative inbox on a Friday afternoon is a today problem, and it needs a today answer.

**Make it more useful.** Make it a channel-wide safety property, not a model behavior. Every patient-writable surface displays "not for medical emergencies — call 911." Inbound messages matching an urgent-symptom keyword list escalate immediately with an audible alert, an automatic acknowledgment to the patient telling them what to do instead, and a logged escalation with a same-day human-review SLA. After-hours inbound gets an immediate auto-reply naming the on-call or emergency path.

**Alternate/better solutions.**

1. *Simpler mechanism:* keyword list plus standard auto-reply plus an SLA. No classifier, no model, auditable, and defensible.
2. *Do not offer free text in beta:* structured replies only (confirm, cancel, reschedule, call me). The hazard is created by the affordance; removing the affordance removes the hazard.
3. *Route by hours:* the highest-risk window is when nobody is watching, and an unconditional after-hours auto-reply is the single most protective control available.

**Reframe.** Not "refuse clinical questions and route them" but: **an administrative channel must be provably safe when a patient uses it for something urgent — including at 11pm on a Saturday.**

**Gaps it leaves.** Urgent content described without keywords ("I don't feel right"). Clinical content typed into a form's free-text box, which no message-level router sees. The medico-legal record of what the practice knew and when. Non-English urgent messages. Who is on the receiving end of the escalation in a practice with no after-hours staffing, which is most of them.

## F6. Accessible multilingual intake

**Verdict: essential-core**, with the machine-translation component deferred. The beta overview commits to patients completing administrative outcomes without speaking English; that commitment is unmet unless language is a first-class attribute driving every form, message, and appointment.

**Make it more useful.** Professionally translate the four canonical documents and the ~50 intake labels into the practice's top one or two languages. Make `preferred_language` a patient attribute that automatically selects message templates, form versions, and an **interpreter-required** flag on the appointment. Machine translation is permitted only for non-authoritative help text and must be visibly labeled as such — never for a consent, an estimate, or a clinical instrument.

**Alternate/better solutions.**

1. *Simpler mechanism:* language field + a human-translated document set + an interpreter flag. That is the whole capability, and it is far more legally defensible than any translation layer.
2. *Phone interpreter integration at the desk:* a one-tap conference-in number on the patient header solves comprehension in the moment, where forms cannot.
3. *Assisted entry as the true fallback:* staff complete the intake by conversation. This must be a first-class, well-instrumented path rather than a failure state — and it is the path most limited-English patients will actually use.

**Reframe.** Not "translate the intake experience" but: **language is a property of the patient, not of a screen — every artifact the practice produces should already be in it, including the statement.**

**Gaps it leaves.** Literacy as distinct from language; read-aloud only helps if it exists in the right language. Deaf and hard-of-hearing patients and ASL interpretation, which is an ADA obligation and is absent. Which translation is legally authoritative for a consent. Family members interpreting — common, discouraged, and unmodeled. Downstream artifacts (statements, Good Faith Estimates, denial letters) remaining English-only, which silently voids the commitment after the visit.

## F7. Warm front-desk handoff

**Verdict: valuable-enhancer**, over an essential-core principle: **no patient-facing surface may dead-end.**

**Make it more useful.** One persistent "Get help" control on every patient surface that creates a callback task carrying the page, the field, the appointment, and a plain-language description of what the patient was trying to do — plus a stated callback window. In a three-person practice the honest handoff is a scheduled callback, not live chat, and promising chat that nobody staffs is worse than not offering it.

**Alternate/better solutions.**

1. *Simpler mechanism:* a "Call us" button plus a logged "patient requested help at step X" event. The practice's phone already works.
2. *Reverse the direction:* staff proactively call patients whose previsit has been stalled at the same step for 24 hours. Abandonment telemetry becomes outbound work, which recovers the patients who would never have asked for help.
3. *Guarantee the desk fallback and say so up front:* "anything you don't finish, we'll do together when you arrive." This converts friction into a non-event and measurably increases completion, because patients stop fearing they will get it wrong.

**Reframe.** Not "hand off from copilot to a human" but: **every patient surface must be able to say, truthfully, what happens next if the patient stops right now.**

**Gaps it leaves.** Handoff outside business hours. The callback queue nobody works, which is the realistic decay. Handoff context containing PHI in a transcript. The patient who needs help *now* to keep tomorrow's appointment. Handoff for a proxy acting on someone else's behalf. No path from handoff back into the task once resolved.

## F8. Per-interaction decision record

**Verdict: defer** as an AI-specific artifact; the general principle — **field-level provenance on every write** — is essential-core and should be built now, model or no model.

**Make it more useful.** One uniform provenance stamp per field write: `(source, actor, method, timestamp, version)`. AI later becomes just another value of `source`. Building an AI-specific ledger guarantees two provenance systems that disagree, which is exactly the outcome the packet's whole provenance thesis exists to prevent.

**Alternate/better solutions.**

1. *Simpler mechanism:* Frappe's native version and audit trail plus a `source` column. No new artifact class.
2. *Snapshot the rendered submission as a PDF:* for a dispute, what the patient saw matters more than the exchange that preceded it — and it is one artifact instead of a linked graph.
3. *Sampled retention:* keep full detail for a small percentage of interactions plus 100% of escalations and disputes, rather than everything. Preserves evaluation value while collapsing the PHI surface the doc worries about.

**Reframe.** Not "record every AI exchange" but: **every value in the record should be able to say who put it there, how, and when — the same way, regardless of what put it there.**

**Gaps it leaves.** Direct conflict between evidentiary retention and minimum-necessary retention, raised in the doc and unresolved. Provenance surviving merges and corrections. Who may read decision records, and whether they are producible on a patient request. Exports carrying PHI into analytics. Provenance for values sourced from a payer rather than a person, which is most coverage data.

---

# 7. Arrival Presence Detection

**Cluster note.** This document is the clearest case of enterprise-shaped thinking in the packet. A small ambulatory practice has a receptionist who can see the front door from her chair. The arrival problem it actually has is not *detecting* presence — it is (a) knowing which arrivals will take desk time, (b) telling waiting patients about delays, and (c) applying a late policy consistently without an argument. The privacy engineering here is genuinely excellent and should be preserved as platform-level principle; the sensing should not be built.

## G1. Appointment-scoped geofence invitation

**Verdict: defer.** Consent, radius configuration, activation windows, and revocation machinery to replace a thirty-second interaction the receptionist is already having. The one real use case — patients waiting in cars — is served by an explicit action rather than passive sensing, and an explicit action is more trustworthy besides.

**Make it more useful.** If ever built, frame it as "text me when you arrive" — an intentional check-in aided by location, not automatic detection. Patients tolerate a system that helps them do a thing; they resent a system that watches for them.

**Alternate/better solutions.**

1. *Simpler mechanism:* "Reply HERE when you arrive," sent at appointment time. Zero permissions, universal device support, unambiguous patient intent, and it produces a cleaner arrival record than any sensor because it is an assertion rather than an inference.
2. *Lobby QR poster* linking to the check-in page. No app, no location, no consent flow, and it works for walk-ins too.
3. *Let the car be the waiting room:* staff text the patient when the room is ready. Arrival becomes asserted, and the practice gains lobby capacity — which was the actual goal.

**Reframe.** Not "detect that the patient has arrived" but: **give the patient a one-tap way to say 'I'm here' and a reason to want to.**

**Gaps it leaves.** The patient who has arrived but is not ready to be checked in. Multi-tenant medical office buildings where the geofence covers four other practices. Adjacent-suite and street false positives. The accompanying family member's phone triggering instead. Patients who arrive early and sit in the lot deliberately.

## G2. Battery-aware boundary sensing

**Verdict: defer.** This is an implementation detail of a deferred feat and carries no independent beta value.

**Make it more useful.** If built, state one hard rule that makes the privacy story simple enough to put on a sign: **no location is collected outside a ±60-minute appointment window, and never in the background when the app is not the active check-in surface.**

**Alternate/better solutions.**

1. *Foreground-only check-in* — the patient opens the page on arrival. No background location model exists to tune.
2. *Wallet pass with an OS-level location reminder* — the operating system nudges the patient to check in without the practice ever receiving a coordinate.
3. *Guest Wi-Fi join as the arrival hint* — hardware the practice already has, no permission dialog, works on every device, and the signal is genuinely indoor.

**Reframe.** Not "sense the boundary efficiently" but: **the cheapest arrival signal is the one the patient volunteers.**

**Gaps it leaves.** OS throttling and delivery latency. Low-power mode. The app terminated by the user or the OEM battery manager. Permissions revoked between visits with no way to detect the degradation. Behavioral divergence between iOS and Android that staff will experience as randomness.

## G3. Lobby BLE confirmation

**Verdict: questionable** — not merely mistimed but likely never justified at this archetype. Beacon hardware, rotating identifiers, a resolver service, key lifecycle management, anti-replay controls, and signage, deployed into a six-chair lobby, to establish a fact a printed QR sticker establishes for free and more reliably.

**Make it more useful.** The only version worth considering is a multi-suite building where the practice occupies a floor and wayfinding is a genuine patient problem — and even there, wayfinding is the value, not arrival.

**Alternate/better solutions.**

1. *Simpler mechanism:* the receptionist. She is already there, already looking, and already accurate.
2. *A printed QR sticker at the desk* — indoor specificity, zero infrastructure, no privacy surface, no batteries, no key rotation, and it degrades gracefully to "ask the receptionist."
3. *A tablet in the lobby* running the same check-in page, if a self-service option is genuinely wanted.

**Reframe.** Not "confirm indoor presence" but: **the practice needs to know the patient is here — which is what walking up to the desk already means.**

**Gaps it leaves.** Patients with Bluetooth off (many). Hardware maintenance and battery replacement as a task assigned to a practice with no IT. Spoofing and replay. Multi-suite buildings. Disclosure signage requirements. Accessibility for patients who cannot approach the beacon zone. The failure mode where staff trust a board that is silently wrong.

## G4. Evidence-shaped arrival state

**Verdict: differentiator** — and it should be **kept even though every sensor is deferred.** The moment desk, kiosk, QR, and SMS arrival paths coexist, knowing *how* arrival was established matters for wait-time honesty, late-policy disputes, and no-show defensibility. This is the good idea in this document.

**Make it more useful.** Four provenance values are enough: staff, self-service (kiosk/QR), patient assertion (SMS/link), sensor. Two timestamps are enough: `asserted_at` and `verified_at`. The only confidence distinction a small practice needs is "the patient says they're here" versus "we've seen them" — collapse the confidence dimension into that.

**Alternate/better solutions.**

1. *Simpler mechanism:* an `Arrival Event` doctype with source, actor, and timestamp; the appointment shows the latest. No confidence score, no decay model.
2. *Encode provenance in the status vocabulary:* "Self-checked-in" and "Checked in at desk" as distinct statuses. The model becomes visible without any metadata, and staff learn it by reading the board.
3. *Derive it from the record owner* — Frappe already stores who wrote the row — and drop the explicit field entirely.

**Reframe.** Not "attach evidence to arrival" but: **wait times and no-show rates are only as honest as the timestamps behind them — so record who said the patient was here, and when.**

**Gaps it leaves.** Corrections — a patient checked into the wrong location or the wrong appointment. Arrival for the wrong one of two same-day appointments. Retroactive end-of-day arrival entry, which is extremely common and destroys wait-time metrics silently. Arrival before the practice opens. **Left without being seen**, which is a distinct and consequential state with no representation anywhere in this cluster.

## G5. Inclusive arrival ladder

**Verdict: essential-core**, and it should be the *only* arrival feat in the beta. It is also where the beta overview's equity commitment either holds or fails in the most visible possible place: the front door.

**Make it more useful.** All four paths write the identical event. Make the desk path one keystroke from the day view — that is 95% of arrivals and it must be the fastest thing in the product. Default new practices to desk + QR poster; enable kiosk only where hardware exists. Never make a self-service path a precondition for anything.

**Alternate/better solutions.**

1. *Simpler mechanism:* desk plus a QR poster. Two mechanisms, 100% of patients, no hardware.
2. *Model the paper sign-in sheet honestly* as `source = staff, method = transcribed`. Practices will keep using it; the data model should not pretend otherwise.
3. *Greeting as arrival:* staff mark arrival when they greet the patient, which is the act that already occurs. The feature is a keystroke, not a workflow.

**Reframe.** Not "offer multiple arrival channels" but: **arrival is one event with several ways to say it — and the fastest way must be the one the receptionist uses forty times a day.**

**Gaps it leaves.** Patients who arrive and are never marked at all, which is the largest source of metric error. A family of three arriving together as one act. Companions and interpreters arriving separately. Drop-off patients. Patients who leave before being seen. Arrival at the wrong location in a two-site practice.

## G6. Flow-aware patient updates

**Verdict: valuable-enhancer**, and the *delay notification* half is the genuinely differentiating idea in this document. Telling waiting patients that the provider is running twenty-five minutes behind is the highest-satisfaction, lowest-cost action a practice can take, and almost nobody does it.

**Make it more useful.** Make delay broadcast a **one-click staff action** — "running 20 min behind" messages today's not-yet-arrived patients — rather than something inferred from a flow board that does not exist yet. This is usable on day one, requires no rooming infrastructure, and is the feature a practice will tell other practices about. Include the patients already in the lobby, who need it most and are hardest to reach.

**Alternate/better solutions.**

1. *Simpler mechanism:* the one-click delay broadcast alone, with no flow board at all.
2. *Post the wait publicly:* a lobby screen or whiteboard showing current delay. No messaging, no consent evaluation, immediate.
3. *Auto-detect and propose:* derive the running delay from actual room-in times and offer staff a pre-drafted message to approve — automation that keeps human authority, which matches the constitution's stance.

**Reframe.** Not "keep patients informed of flow status" but: **nobody should sit in a waiting room without knowing how long — and telling them costs one click.**

**Gaps it leaves.** Reaching patients already seated. A morning delay cascading into the afternoon. Patients who cannot leave work and need the delay *before* they depart. The expectation problem — a broadcast "20 minutes" becomes a promise. What "room ready" means in a practice with three rooms and no rooming system. Patients who respond to a delay message by asking to reschedule, with no described handling.

## G7. Policy-aware exception routing

**Verdict: valuable-enhancer.** Late arrivals produce daily staff-patient conflict, and the value of encoding the policy is *consistency* — staff stop negotiating case by case and stop absorbing the resentment personally.

**Make it more useful.** One setting: grace minutes by appointment type. At grace+1, the appointment surfaces with three buttons — **Fit In / Reschedule / Mark No-Show** — each carrying a pre-written patient message. That is the whole feature, and the pre-written message is the part that saves the staff member's afternoon.

**Alternate/better solutions.**

1. *Simpler mechanism:* a countdown badge on the day view; the decision stays entirely human, with no rules to configure.
2. *Solve it upstream:* a T-2h "leave by 9:35 to arrive on time" message with travel time reduces lateness more than any downstream policy, and it is a message, not a subsystem.
3. *Make lateness a booking event:* the same message that marks a patient late offers the next available slot, turning a conflict into a rebooking.

**Reframe.** Not "route late arrivals by policy" but: **the practice should make the same decision every time and never have to argue about it at the desk.**

**Gaps it leaves.** Who decides — provider versus desk — and what happens when they disagree. Lateness caused by the practice's own delay, which must not count against the patient. Repeat-offender policy, documentation, and dismissal. A late patient who is the last of the day. Late arrival for a linked family block. No-show fees, their collectability, their legality by payer, and whether a Medicaid patient may be charged one at all.

## G8. Purpose-limited location ledger

**Verdict: defer** with the sensing — but the underlying principle should be **promoted out of this cluster** into the platform trust plane as a general retention-class rule.

**Make it more useful.** Generalize into named retention classes applied to every data type in the product: raw sensor observations (24 hours), evidence records (7 years), aggregates (indefinite), message bodies (policy-set), card images (policy-set). One retention system, uniformly applied, is worth far more than a bespoke location ledger.

**Alternate/better solutions.**

1. *Simpler and strictly better — never store coordinates at all:* evaluate the geofence on the device and post a boolean. There is nothing to retain, nothing to leak, nothing to subpoena, and nothing to explain on the signage. This should replace the ledger idea outright.
2. *Store the assertion plus a hash of the evidence*, not the evidence.
3. *On-device only*, with staff seeing "patient asserts arrival" and no location data ever crossing the network boundary.

**Reframe.** Not "retain location data under policy" but: **do not collect what you would have to justify — a boolean beats a coordinate and a deletion schedule.**

**Gaps it leaves.** Discovery and subpoena of whatever is retained. Disputes about whether a patient arrived, where the deleted raw data was the evidence. Backups holding data past its deletion date. Vendor SDKs collecting location independently of the application's policy — the most likely real-world breach of this design.

---

# 8. Cluster composition feats (synthesis document)

The synthesis document proposes seven compositions rather than atomic capabilities. Verdicts here are about whether the *composition* should be built as an explicit joint, since each individually reviewed member already carries its own verdict.

- **One funnel, three surfaces** — **essential-core.** The chain "readiness → constraints → consent" evaluated once, consumed everywhere, is the architectural claim that makes this product different from an assembly of modules. Build the chain; drop "three surfaces" (it should be surface-agnostic, and naming three invites a fourth).
- **Readiness-gated self-service** — **differentiator.** Correct, and the phrase "gaps route to staff queues with reasons instead of failing silently" is the best single sentence in the packet. The caution: *gated* must never mean *denied* — it means "assisted."
- **Risk-shaped outreach** — **defer.** Depends on B3, which defers. The consent, quiet-hours, and channel-preference half is already essential-core in C2/C3 and needs no risk input.
- **Vacancy recovery as workflow** — **differentiator.** Keep, and simplify per B2. This is the composition most likely to pay for the beta.
- **One patient surface, two backends** — **essential-core as a routing contract**, defer as an app. The rule "every datum lands in its owning system and openPractice never persists an authoritative copy of clinical content" must be enforced from day one on the *web* surface, or it will be retrofitted badly later.
- **AI-assisted completion with receipts** — **defer.** Per section 6; the receipts (provenance) are essential-core and should be built without the AI.
- **Presence without friction** — **valuable-enhancer**, reduced to its ladder and provenance halves (G5, G4). The sensing half defers.

**Composition verdicts:** essential-core 2, differentiator 2, valuable-enhancer 2, defer 1.

---

# 9. Missing feats

Named proposals for what the cluster needs to be complete. Ordered roughly by how badly a small practice would feel the absence.

- **Front-Desk Day Cockpit** — one screen showing today: every appointment with arrival state, readiness state, balance, unread reply, and previsit status, sorted by which ones need a human. Everything else in the product is a drill-down from this screen. Its absence is the cluster's single largest hole: seven documents describe seven capabilities and none describes the place a receptionist actually lives.
- **Walk-In And Same-Day Access** — an entry path with no prior appointment: create or find identity, decide whether capacity exists, slot into a same-day queue, and run a compressed readiness path at the desk. Walk-ins are a meaningful share of small-practice volume and appear in none of the seven documents.
- **Phone Console And Call Disposition** — the phone is the dominant channel for a small practice and is invisible across the entire cluster. Caller-ID screen-pop to the patient, one-click booking/message/callback from the call, and a required disposition that becomes a work item or a logged outcome.
- **Recall And Overdue-Care Outreach** — the demand engine: due-date-driven outreach for annual visits, follow-up intervals, and chronic-care checks, administered here from clinical recall dates owned by the EHR. It feeds the waitlist, the campaigns, and the schedule; without it, the practice's schedule is purely reactive.
- **Reschedule As A First-Class Operation** — most systems model reschedule as cancel-plus-create, which orphans the previsit plan, the estimate, the eligibility result, the queued cadence, and the issued link. Reschedule should carry all of it forward and invalidate exactly what genuinely changed.
- **Patient Merge Aftermath And Reversal** — the reconciliation that follows a merge: appointments, in-flight claims, balances, signed forms, issued links, live threads, portal credentials, and sent statements — plus a 30-day reversal that restores both records intact. Currently the packet detects duplicates and stops.
- **Coverage Change Detection Between Booking And Visit** — automatic re-verification at T-3 days and at arrival, with a named blocker when a plan has terminated or changed. A plan that ended on the first of the month is the most common revenue leak in ambulatory practice, and the packet verifies coverage once.
- **Household And Family Visit Coordination** — booking, checking in, preparing, signing, and paying for a family block as one act, against one guarantor account. This is how families actually experience a practice and nothing in the cluster models it.
- **Provider Absence And Mass Schedule Disruption** — the doctor is out sick: reschedule twenty-two patients, notify all of them, offer alternatives, and track who has landed somewhere. This is the worst operational day a small practice has, it happens several times a year, and no feature in the packet touches it.
- **Interpreter, Accessibility, And Accommodation Scheduling** — language, ASL, wheelchair access, service animal, extra time, and a companion — modeled as appointment requirements that constrain booking, trigger vendor arrangements, and appear on the day view. Referenced obliquely in AI intake and equity, absent from scheduling.
- **Self-Pay And Good Faith Estimate Lane** — a first-class uninsured path: sliding scale or standard self-pay pricing, a compliant Good Faith Estimate before service, and a dispute-evidence trail. The beta overview flags it; the front-office cluster assumes coverage everywhere.
- **Minor-To-Adult Authority Transition** — automatic expiry of guardian roles at the age of majority, re-consent, communication re-routing, and handling of confidential adolescent services in the window before it. Currently an unscheduled data-integrity bomb.
- **Referral-In Intake** — for any specialty practice, most new patients arrive as an inbound referral (fax, portal, direct message), not a phone call. Referral → identity → coverage/authorization → appointment offer → status back to the referring office. The beta packet owns referral administration; the *front-office funnel* has no entry point for it.
- **Shift Handoff And Open-Items Log** — the person who leaves at 2pm must hand over what is unresolved. A named, checkable handoff list is how a compressed-role office avoids dropping things at the seam between people.
- **Nurse-Visit And Standing-Order Booking** — injections, immunizations, blood pressure checks, wound care: appointment types that consume nurse rather than provider capacity and follow different rules. Common ambulatory volume with no representation in the constraint model.

---

# 10. Cross-feat gaps

Seams where work falls through between feats that were each specified in isolation.

- **Readiness never gates a waitlist offer.** B2 makes offers to whoever is best-matched; A6 knows the patient needs a prior authorization that cannot be obtained in ninety minutes. Nothing connects them, so vacancy recovery will systematically generate denials.
- **Merge is specified upstream of everything and integrated with nothing.** A2 produces a merge; appointments, threads, links, signatures, balances, and claims are all downstream and none of the seven documents says what happens to them.
- **Two consent models will diverge.** Consent is captured at registration (A5, C3) and separately requested by the app and its permission ladder (D4). If they are not the same records, a patient can opt out in one place and keep receiving messages from the other.
- **A fully prepared patient still queues at the desk.** E6 publishes readiness; G5 records arrival; nothing tells the front desk "this one is done, wave them through." Without that, the entire previsit investment produces no visible benefit to either party, which is how these programs die.
- **Reschedule silently invalidates four artifacts** — previsit plan, estimate, eligibility, and queued cadence — and no feat owns the invalidation.
- **Inbound "cancel" has no defined authority.** C1 threads it, B1 owns the schedule, and no document says who writes the cancellation, whether it applies a late-cancel policy, or what happens at 11pm when no human will see it before the slot passes.
- **Three timestamp semantics feed one metric.** Arrival provenance (G4), no-show classification (B3), and access metrics (B6) each define "did the patient come" differently, and retroactive end-of-day arrival entry corrupts all three.
- **Two provenance systems.** F8 proposes an AI decision record; A4/E4 imply field- and document-level provenance. Building both guarantees they disagree in exactly the dispute where it matters.
- **No household object anywhere.** Registration models persons and roles, scheduling models individuals, communications model recipients, links model appointments — and every real front office thinks, speaks, and bills in families.
- **Language and accommodation stop at the intake form.** F6 makes language first-class for intake; scheduling never books an interpreter, communications never selects a template by language, and statements and estimates remain English.
- **Nobody owns "the patient is standing here and something is wrong."** Every feat has a happy path and a queue; the desk-side real-time exception — no card, wrong insurance, no guardian present, unmerged duplicate — has no owner and is precisely where a small practice spends its stress.
- **The self-service surfaces have no staff mirror.** Staff cannot see what the patient sees, complete it on their behalf, or resume a stalled task — which makes the assisted-channel equity commitment unimplementable as specified.

---

# 11. Intuitiveness assessment

**How the cluster currently feels.** Seven documents read as seven product lines: a registration product, a scheduling product, a communications product, an app, a previsit experience, an AI assistant, and a sensor platform. That is the exact fragmentation the overview criticizes incumbents for — the packet has reproduced the vendor org chart in its own document structure. A three-person office does not experience seven products; it experiences one day.

**Where cognitive load concentrates.** Three moments, and the packet's attention is inversely proportional to their weight:

1. **On the phone, booking.** Someone is waiting while the receptionist decides type, provider, duration, and whether the patient is cleared. This is the highest-pressure interaction in the practice and it is served by B1's constraint engine, which as written adds decisions rather than removing them.
2. **At the desk, checking in.** Card, forms, copay, and the exception nobody planned for, with a lobby watching. This moment has no owning feat.
3. **End of day, cleanup.** Unworked replies, unresolved blockers, unbooked follow-ups, unposted arrivals. The packet emits work into many queues and never says how the day is closed.

**Vocabulary is leaking architecture into the UI.** "Registration-readiness contract," "communication decision record," "evidence-shaped arrival state," "previsit completion percentage" are all correct engineering names and all wrong product names. Staff will say *ready*, *sent*, *here*, *done*. If the interface uses the architecture's words, the practice will build a private translation layer in its own head and then in a spreadsheet.

**The one mental model to adopt.** **One day, one patient, one list.**

- **One day** — the cockpit is the home screen. Every appointment shows arrival, readiness, balance, and whether a human is needed. The list is sorted by who needs a person, not by time.
- **One patient** — everything about a person is one page: who's involved, what's true, what's owed, what's been said, what's next. No module tabs.
- **One list** — the difference between what a visit requires and what is known is a single computed list, rendered to the patient on their phone, to the kiosk, to the desk, and to the biller. There is not a patient list and a staff list; there is one list with different closers.

Everything else in this cluster — consent, constraints, provenance, sensing — should be *invisible enforcement underneath that model*, never a surface. The test for any beta feature: can the newest person at the desk explain it to a patient in one sentence without using a product noun?

---

# 12. Top 3 reframings

1. **From "readiness as a contract you query" to "readiness as the sort order of the day."** Readiness should not be an API other domains consult; it should be the thing that decides which appointment appears at the top of the receptionist's screen and what button sits next to it. A contract that produces no visible ordering is invisible; an ordering that is wrong is immediately corrected. This single move converts the packet's best architectural idea into its best product idea, and it makes the front-desk cockpit fall out for free.

2. **From "collect data from patients" to "prove what we already know is still true."** Every intake surface in this cluster is designed as a collection surface with an adaptive-requiredness layer bolted on to reduce the burden. Invert it: the default interaction is confirmation, the only new questions are the ones the *claim* requires and nobody has answered, and staleness is a timestamp rather than a rule. This eliminates the adaptive-requiredness engine, the completeness coach, most of the AI interpreter, and the duplicate-entry problem simultaneously — four feats collapse into one property on every field.

3. **From "channels" to "one task list per visit, rendered wherever the patient is and closable by anyone."** App, mobile web, kiosk, SMS, phone, and desk are rendering decisions, not capability decisions. Build the visit task list once, give it a URL that needs no account, let staff render and close it on the patient's behalf, and the equity commitment, the kiosk parity requirement, the app-versus-web debate, the cross-device resume feature, and the assisted-channel fallback all resolve as consequences rather than as features. It also makes the app falsifiable: if it does not beat the plain link, it does not ship.

*Runner-up, and the cheapest of all:* **from "predict who will not show up" to "drive every appointment to an explicit yes or no by T-24h."** No model, no bias audit, no cold start — and the residue is a call list, which is something a person can actually work.

---

## Relationships

Reviews [Synthesis: Front Office And Access](../brainstorm/openpractice-feature-list-synthesis-front-office-and-access.md) and its seven atomic members, read against [Small-Practice Beta Feature Analysis Overview](../brainstorm/openpractice-small-practice-beta-overview.md). No brainstorm document is modified by this review; verdicts are non-normative and contradiction with the source packet is intentional.
