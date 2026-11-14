# Feat Review: Practice Administration

Status: brainstorm
Kind: review
Summary: the practice-administration cluster's forty candidate feats are mostly the right nouns modeled at the wrong altitude — an enterprise master-data and governance vocabulary aimed at a three-person office — and the review keeps the readiness graph as the headline differentiator, collapses configuration-promotion machinery into scheduled changes plus undo, and names the missing money features (retro-enrollment claim holds, locum and supervising billing arrangements, provider onboarding and departure runways) that decide whether the beta practice actually gets paid.

Topics: openpractice-feat-review, practice-administration
Captured: 2026-08-26

## How to read this review

Forty feats across six atomic docs and one synthesis. Verdicts use five bands:

- **essential-core** — the beta is broken or dangerous without it;
- **differentiator** — a defensible reason a practice picks openPractice over Tebra, AdvancedMD, or a credentialing service;
- **valuable-enhancer** — real value, not on the critical path;
- **questionable** — the underlying need is real but this framing is the wrong shape;
- **defer** — preserve the research, build nothing in beta.

The recurring critique across every doc: the modeling vocabulary is sound and the **product vocabulary does not exist yet**. An office manager will never type "effective-dated affiliation," "administrative evidence object," or "practice configuration set." Every feat below is judged on whether it survives contact with a person who has forty minutes between patients.

---

## 1. Practice Structure And Service Catalog

### 1.1 Small-practice setup template

**Verdict — essential-core.** Setup abandonment is the single most likely cause of beta failure; a practice that cannot get to a bookable slot on day one never reaches claim one. But "template" undersells it: a template still asks the office manager to know what a billing entity is, and she does not, because her accountant handled the W-9 and a credentialing service filled out the CMS-855.

**Make it more useful.** Ship exactly one template — solo-or-small ambulatory, one location, one tax ID — and make every other shape a later "add" action rather than a fork at the start. Cap first-run setup at the fields that gate a bookable appointment: practice name, address, hours, NPIs, providers, three appointment types. Everything else (merchant, accounting destination, statement footer, payer list) should be requested *at the moment it first blocks something*, with the blocking thing named: "To send this claim, openPractice needs the group NPI." Setup should be provably finishable in under 30 minutes with the practice's existing paperwork on the desk.

**Alternate/better solutions.**
- *(Simpler)* **Paperwork-first bootstrap.** Ask for four artifacts the practice already has: the W-9, one previous CMS-1500 or 837 sample, one payer remittance, and a staff roster. Parse them and pre-fill everything — group NPI, tax ID, pay-to address, billing provider name, payer identifiers, place of service, at least one rendering provider — then ask the office manager to confirm, not author. A prior claim is a complete, authoritative statement of the practice's own billing structure; it is the cheapest master-data import in healthcare and nobody uses it.
- **NPPES-seeded setup.** Type the group NPI. Pull organization name, authorized official, taxonomy, practice location, and other identifiers from the public NPI Registry API. Type each provider NPI; pull name, credential, taxonomy, and address. Setup becomes five NPI lookups and a confirm screen.
- **Setup as a conversation with a receipt.** A single scrolling Q&A ("How many places do you see patients?" → one → the location object is created silently) that emits a printable one-page "How your practice is set up" summary. The office manager never sees an entity diagram; she sees a page she can hand to her accountant.

**Reframe.** Not "configure the practice" but **"teach openPractice what your last claim already knows."** Setup is confirmation of facts the practice has documented elsewhere, never original authorship.

**Gaps it leaves.** A template assumes the practice's structure is stable at setup, which is exactly when it isn't: a practice mid-way through adding a second tax ID, a solo doc incorporating as a PC, a practice whose "location" is two suites in one building with different suite numbers on file with different payers. No path for a practice that bills under a management company's TIN. No handling of a practice that starts as cash-pay and adds insurance three months in — which is a *different* setup order entirely.

### 1.2 Effective-dated organization graph

**Verdict — essential-core, but the concept must never surface.** Without date-scoped relationships, a mid-year fee change or provider start date silently rewrites history and the practice's own claims stop reconciling. But effective dating exposed as a modeling concept produces the single most-hated screen in every incumbent PM system: a grid of overlapping date ranges nobody dares edit.

**Make it more useful.** Two UI primitives, no third. (1) **"Starting on ___"** — every change field has an optional date; blank means now. (2) **"As of ___"** — a global date toggle that renders every screen as it stood or will stand on that date. Ship a "What changes on…" list: a calendar of scheduled future changes with an owner and a one-click cancel. Never render a date-range table to a user. Forbid the words "effective dated," "version," and "supersede" from the interface entirely.

**Alternate/better solutions.**
- *(Simpler)* **Append-only change log with time travel.** Do not model bitemporality. Store an immutable ordered stream of change events per object with an `applies_from` timestamp, and compute the state at any date. Frappe's existing Version doctype already captures field-level change history for free; the incremental work is a resolver and an "as of" context, not a new modeling layer. This gets 95% of the value at 20% of the schema.
- **Freeze-at-use snapshotting.** Instead of asking every consumer to resolve context by date, have appointments, charges, and claims *copy* their resolved administrative context (billing entity, pay-to, rendering NPI, place of service, price) at creation and never re-resolve. History becomes immutable because the financial record carries its own context, and configuration edits are then safe by construction. This is how a small practice actually thinks: "the claim says what it said."
- **Change-with-consequences preview.** Before saving any structural change, show the blast radius in practice language: "Starting Mar 1: 14 already-booked appointments will bill under the new tax ID. 3 of them have authorizations tied to the old one." The date model earns its keep by being *visible as consequences*, not as dates.

**Reframe.** **"You never edit the practice; you schedule a change, and you can always look at any day."**

**Gaps it leaves.** No stated policy for the retroactive case, which is the common one: the payer approves an enrollment with an effective date three months in the past, or the practice discovers the address was wrong on 200 claims. Retroactive insertion into an effective-dated graph is a correction workflow, and the doc's own open question admits it is unresolved. Also silent on which changes require *re-doing* downstream work (a changed tax ID invalidates authorizations and requires payer notification) versus merely applying forward.

### 1.3 Administrative service catalog

**Verdict — essential-core.** This is the actual joint of the system — scheduling, estimation, authorization, and charging all need one shared answer to "what are we doing." Getting it right is the difference between one entry and four. The risk is over-abstraction into a shadow terminology system, which the doc correctly flags but does not defend against.

**Make it more useful.** Define the offering by what it must produce, not by what it could model. Minimum viable offering: patient-facing name, duration, default resources, default CPT/HCPCS set with modifiers, default place of service, self-pay price, whether authorization is typically required, whether a new-patient path applies, and which providers may deliver it. Seed 12–20 offerings for the design partner's specialty from real CPT usage in their prior claims file rather than shipping an empty catalog. Explicitly forbid diagnosis linkage, medical-necessity rules, and clinical prerequisites in the object — those belong to the EHR and to coding.

**Alternate/better solutions.**
- *(Simpler)* **Appointment type with a price tag.** Skip the separate catalog entity in beta. Extend the appointment type with billing defaults and a self-pay price. One object, one edit screen, no mapping table. Split into a catalog only when the practice has offerings that are not appointments (records fees, forms fees, supplies, injections billed without a visit) — which is a real but later need.
- **Learn the catalog from history.** Import the prior year's charge lines, cluster by CPT+modifier+duration, and propose the top 20 as offerings with observed average duration and observed self-pay price. The practice edits names. Zero authoring.
- **Two-tier: "what we book" vs "what we bill."** Keep a tiny bookable list (what a patient or referrer can ask for) mapped many-to-one onto a billing recipe. This prevents the catalog from bloating every time marketing wants a new service name, without duplicating billing rules.

**Reframe.** **"An offering is a saved answer to: how long, who can do it, what does it cost, what do we bill, what does it need first."**

**Gaps it leaves.** Nothing on price changes mid-cycle — an offering price edited in June must not reprice a May estimate or a Good Faith Estimate already given (see missing feat *Price-by-Date-of-Service*). Nothing on bundled or multi-unit offerings, add-on codes, or the "same visit, two services" case. No handling of offerings that are payer-specific (some payers won't cover the code the practice normally bills for this service), which is where the catalog meets the contract atomic. No modeling of a non-billable offering (nurse visit, no-charge recheck) even though small practices run them constantly.

### 1.4 Operational readiness contract

**Verdict — differentiator.** This is the strongest idea in the whole cluster and, together with 2.5 and 7.1, the reason a practice would choose openPractice. Incumbents surface configuration errors as validation failures at the worst possible moment — claim rejection, three weeks after the visit. A dated, explainable "can this be scheduled/billed and what's missing" verdict is genuinely novel at this price point.

**Make it more useful.** Make it *forward-looking and unsolicited*. The value is not answering a query; it is telling the office manager on Thursday that Tuesday's schedule has a problem. Every readiness failure must produce (a) a plain-language sentence, (b) a named owner, (c) a proposed next action with a button, and (d) an explicit expiry — "this becomes a hard block on May 1." Distinguish three severities only: **blocks booking**, **blocks billing** (book freely, hold the claim), **informational**. Never invent a fourth.

**Alternate/better solutions.**
- *(Simpler)* **A single "Ready for Tomorrow" page.** One screen, run nightly, listing every appointment in the next 14 days that cannot currently be billed, grouped by cause, with a fix button. No contract API, no readiness evaluation object — just a scheduled query with good copy. Ship this first; extract the general contract only if a second consumer appears.
- **Readiness as slot color, computed at booking time.** Push the verdict into the scheduling grid itself: a slot that would produce an unbillable visit renders differently and explains itself on hover. Prevention beats reporting.
- **Readiness as a receipt, not a gate.** Never block; always record. Booking an unready slot produces a signed "we knew this and booked anyway, because ___" record that flows to the claim hold queue. This respects the reality that a small practice will see the patient regardless, and converts a fight with the software into evidence.

**Reframe.** **"Tomorrow's schedule is green, or somebody's name is on the reason it isn't."**

**Gaps it leaves.** Doesn't say what happens when readiness *degrades after booking* — a license expires between booking and the visit, an enrollment is terminated, a payer drops the location. That's the common case and needs a re-evaluation sweep plus patient-facing consequences (reschedule? bill self-pay? proceed and appeal?). Also silent on readiness for *non-appointment* actions: can this provider sign an order, be listed as supervising, be named as the referring provider on someone else's claim?

### 1.5 Safe multi-location growth

**Verdict — defer (data model only).** The beta target is one small ambulatory practice; multi-location UI is speculative capacity for a customer that doesn't exist yet. The *schema* must not conflate location with entity — that's covered by 1.2 and is non-negotiable — but time zones, per-location holiday calendars, and department hierarchies are pure cost in beta.

**Make it more useful.** Reduce to the minimum that prevents a rewrite: location as a first-class row, appointments and charges carrying a location reference, place of service resolved per location, and a single "Add a location" action that is allowed to be crude. Skip time zones entirely — assume one practice time zone, store UTC, and revisit when a partner actually crosses zones.

**Alternate/better solutions.**
- *(Simpler)* **Location as an address book entry plus a place-of-service code.** No hierarchy, no departments, no per-location calendars. A second location is a second row that overrides address, POS, and hours.
- **"Opening a location" as a checklist, not a hierarchy.** The real problem isn't modeling a second site; it's the six weeks of work opening one creates — NPPES address add, payer location adds, new POS, sometimes a new CLIA, updated statements and estimates. Ship that as a guided checklist later (see missing feat *Location Opening Kit*) and leave the object model boring.

**Reframe.** **"A second location is a project the practice does once every five years — and it is a checklist, not a data model."**

**Gaps it leaves.** Satellite-opening reality is entirely unhandled: providers are enrolled per location with most payers, so a new site can be fully built and completely unbillable. Nothing on temporary or shared space (a doc renting a suite two days a week), telehealth as a pseudo-location with its own POS rules, or home/facility-based services where the location is the patient's address.

---

## 2. Provider Credentialing, Enrollment, And Directory Lifecycle

### 2.1 Provider evidence inventory

**Verdict — essential-core.** A practice with no credentialing specialist keeps this in a manila folder and a spreadsheet with three expired dates in it. The inventory is cheap to build and prevents a class of expensive surprises (lapsed license, lapsed malpractice, dropped DEA). The risk is that it becomes data-entry theater — twenty fields per credential, entered once, never maintained.

**Make it more useful.** Optimize for *capture speed and self-maintenance*, not completeness. Drag a PDF in; the system proposes type, issuer, number, and expiry from the document, and the human confirms three fields. Track only what actually gates something: state license, DEA where relevant, board certification, malpractice COI, NPI, CAQH ID, Medicare PTAN, CLIA where applicable. Everything else is an attachment with a name. Show the inventory as a single per-provider card with traffic lights, not a table of rows.

**Alternate/better solutions.**
- *(Simpler)* **Expiry-only mode.** Two fields per credential — what it is and when it dies — plus an optional file. The practice gets 90% of the risk reduction from the expiry date alone. Offer full provenance fields as a progressive reveal for practices that get audited or delegate-credential.
- **CAQH export as the seed.** The practice's own CAQH ProView data summary export contains licenses, DEA, education, work history, malpractice, and specialties in one document, already assembled for payers. Parse it once, and the inventory is ~80% populated with zero typing. Since CAQH re-attestation runs on a 120-day cycle, re-importing the export becomes the natural refresh ritual — the practice already has to do it.
- **Primary-source verification links instead of stored copies.** Store the state board lookup URL and license number rather than a scanned certificate; re-check on a schedule. Less PHI/PII at rest, less staleness, and closer to what verification actually means.

**Reframe.** **"Nothing about this provider expires without somebody being told first."**

**Gaps it leaves.** No model for credentials that are *per-location* or *per-facility* (hospital privileges, CLIA, facility-specific DEA registrations). No handling of a credential in renewal limbo — application filed, old one expired, practice still seeing patients. Nothing about locum providers who need evidence tracked for 60 days and then never again, or about a provider whose license is restricted rather than expired, which is a readiness question with no expiry date.

### 2.2 Payer enrollment cases

**Verdict — essential-core.** For a small practice, enrollment is where money is lost silently: a new provider sees patients for ten weeks before anyone realizes the Aetna application was never submitted. A case object with status and dates is the minimum viable memory. The proposed grain — provider × entity × location × payer × product × purpose — is *correct* and also, entered by hand, brutal.

**Make it more useful.** Generate the case matrix, don't ask for it. When a provider is added, create one case per payer the practice already bills (derived from prior remittances), defaulting product and location from the practice. The office manager then *closes* cases she doesn't need rather than creating the ones she does — subtraction is far faster than construction. Collapse the eight-state lifecycle in the UI to four visible states: **not started, waiting on us, waiting on them, done**, with the finer states as detail. Every "waiting on them" case must carry a submission date, an expected-decision date, and a follow-up date that regenerates on contact.

**Alternate/better solutions.**
- *(Simpler)* **A per-provider participation grid.** One screen: providers down, payers across, cells that are green/amber/red with an effective date and a note. The "case" is a click-through detail. Small practices think in grids, not in workflows — this is the artifact a credentialing consultant emails them anyway, and matching it makes the product instantly legible.
- **Enrollment as a countdown to a billable date.** Model the outcome the practice cares about — "Dr. Chen can bill BCBS starting ___" — and treat correspondence, requests, and statuses as evidence supporting or moving that date. The question a practice asks is never "what state is the case in," it is "when can I book her insured patients."
- **Roster-file round trip.** Many payers accept and return a spreadsheet roster for group adds and changes. Generating the payer's roster format from the practice graph, and re-importing the returned file to set statuses and effective dates, replaces most manual case updating with two file operations.

**Reframe.** **"Every provider has a date, per payer, after which their visits turn into money — and openPractice knows it, defends it, and warns before it moves."**

**Gaps it leaves.** The largest omission in the cluster: **retro-enrollment**. Approvals routinely carry retroactive effective dates, and there is no claim-hold-and-release mechanism anywhere in these docs (see missing feats). Also missing: delegated credentialing (where the payer accepts the group's own credentialing and enrollment is near-instant), group-vs-individual enrollment interaction, the "linked to group" step that is separate from individual approval, provider *termination* with a payer, and CAQH attestation lapse silently stalling every pending application at once.

### 2.3 Revalidation and expiry watch

**Verdict — essential-core.** Cheap, high-value, and the thing a small practice most reliably fails at. Medicare revalidation on a five-year cycle and CAQH re-attestation on a 120-day cycle are both invisible until they aren't, and both can suspend billing.

**Make it more useful.** Build a single **calendar of everything that dies**, not per-domain reminders. Escalation ladder by consequence, not by uniform lead time: things that stop billing get 120/90/60/30/14/7-day notices, things that are annoyances get 30 and 7. Every notice must include the action and the link, not just the fact. Critically — snooze must require a reason and a date, and a snoozed item must reappear; the failure mode of every expiry system is silent dismissal.

**Alternate/better solutions.**
- *(Simpler)* **One ICS feed plus one weekly email.** Publish practice expiries as a subscribable calendar and send a Monday digest: "3 things expire in the next 90 days; here they are; here's the link." Zero new UI, works on the office manager's phone, survives her not logging in.
- **Auto-detected cadences.** Don't ask the practice to configure lead times. Infer them from credential type (license → 90 days, malpractice COI → 45, CAQH → 100, Medicare revalidation → 180) and let the practice adjust only if they complain.
- **Recheck instead of remind.** For anything with a public source — NPPES, state license lookup, Medicare enrollment status — poll it rather than trusting the stored date. A reminder about a date the practice typed is only as good as the typing; a recheck catches the license that was suspended off-cycle.

**Reframe.** **"Nothing that keeps this practice paid is allowed to expire quietly."**

**Gaps it leaves.** No coverage of the *cascade*: a lapsed CAQH attestation stalls every pending enrollment; an expired malpractice COI invalidates hospital privileges and several payer participations at once. No coverage of what to do with already-booked appointments that fall past an expiry date — the schedule should be re-swept, not just the credential flagged.

### 2.4 Directory accuracy campaigns

**Verdict — valuable-enhancer.** Directory accuracy obligations are real (plans must verify provider directory data on a rolling cycle, and providers must supply it), and a wrong directory listing costs a small practice new patients. But "campaigns" is enterprise vocabulary for a practice with four providers, and the practice's actual experience is *reactive*: a payer emails a spreadsheet, someone fills it in.

**Make it more useful.** Turn it into a **one-click attestation**: a quarterly screen showing every publicly asserted fact (address, phone, specialty, accepting new patients, panel status, languages spoken) with "still true / fix" per row and one signature. Retain who attested and when. Then make the *output* the feature: generate the payer's requested format from the attested facts.

**Alternate/better solutions.**
- *(Simpler)* **Attest-at-the-source.** Fold directory attestation into the expiry calendar (2.3) as just another recurring item. No campaign object at all — a recurring task with a form.
- **Drift detection against NPPES and payer directories.** Instead of asking the practice to attest, *compare*: pull the public NPI registry record and, where fetchable, the payer's public directory listing, and raise work only when they disagree with the practice's own truth. Attestation becomes exception handling. This is far more useful than a campaign because it catches the wrong data the practice doesn't know about.
- **Publish a canonical practice profile.** Maintain one machine-readable practice/provider profile the practice can point payers, referrers, and its own website at, so accuracy is maintained once rather than per-destination.

**Reframe.** **"Everywhere the world can look us up says the same thing — and we find out when it doesn't."**

**Gaps it leaves.** Nothing on "accepting new patients" being *per payer and per provider*, which is how it actually varies. Nothing on the departed provider still listed in three payer directories six months later, generating calls the practice can't serve. No path for correcting a payer directory error, which is genuinely hard and mostly a phone call — the product should at least track the attempt.

### 2.5 Administrative readiness contract

**Verdict — differentiator.** This is the cluster's headline capability and the sharpest answer to the "why not a credentialing service" question: a service tells you the status; openPractice tells you whether tomorrow works. Overlaps 1.4 and 7.1 and should be *one* mechanism with several inputs, not three feats.

**Make it more useful.** Merge with 1.4 into a single readiness evaluator with pluggable checks. Express every verdict as a sentence with a date and a subject: "Dr. Chen can be billed to UHC at Main St. for office visits starting Apr 12." Provide the negative form as the primary UI: a list of *what is not true yet*. Support "explain this" — clicking a verdict shows the exact evidence and rule that produced it, which is what makes it trustworthy and what makes support calls short.

**Alternate/better solutions.**
- *(Simpler)* **Three booleans on the booking screen.** Can we book? Can we bill it? Do we need auth? Everything else is detail behind a link. A small practice needs three answers, not a contract.
- **Readiness as a queryable date range per tuple.** Rather than evaluating at a point in time, precompute and store billable date ranges per (provider, entity, location, payer, offering). Scheduling then does a cheap range lookup and can answer "when is the earliest I can book this patient with this insurance" — a question no incumbent answers well and one that directly converts to revenue.
- **Readiness diff as the alert.** Only surface *changes* in readiness — the tuple that was green yesterday and is amber today. Steady-state green is noise.

**Reframe.** **"For any patient, insurance, provider, service, and day: yes, no, or here's exactly what's missing and who owns it."**

**Gaps it leaves.** Supervising and substitute arrangements are the glaring hole: an unenrolled NP may be perfectly billable *under* a supervising physician, and a locum may be billable under the absent physician's NPI with the right modifier and within the time limit. A readiness contract that only knows "enrolled / not enrolled" will produce confidently wrong red verdicts and destroy trust in the feature. This must be in scope before the contract ships.

### 2.6 Safe delegation

**Verdict — essential-core (as a constraint, not a module).** Cheap, prevents a genuinely serious failure mode — a practice storing a provider's CAQH or PECOS password in a shared note and a staff member e-signing an attestation. In a 3-person office this happens by default unless the product makes the safe path easier.

**Make it more useful.** Express it as two product rules, not a feature: (1) openPractice never accepts an external portal credential — the field does not exist; (2) any action requiring the provider's personal authority produces a **signature request to the provider** (email/SMS link, 30 seconds, on their phone) rather than a staff checkbox. The staff member prepares everything; the provider taps once. Make the provider's one-tap experience so fast that nobody is tempted to forge it.

**Alternate/better solutions.**
- *(Simpler)* **A "needs the doctor" tray.** One list per provider of everything awaiting their personal act, delivered as a weekly link. No delegation model, no authority graph — just a queue that only they can clear.
- **Credential vaulting by reference.** If portal automation is ever wanted, store nothing: use the provider's own browser session via a guided assist ("we'll open the portal, you log in, we'll fill the form"), so the practice gets the labor savings without the practice ever holding the credential.
- **Recorded delegation with an expiry.** Where a payer genuinely permits a designated staff submitter, capture the delegation as a dated, provider-signed record with a scope and an end date — which is also the artifact an auditor asks for.

**Reframe.** **"Staff do all the work; the doctor does only the signing — in under a minute, from a phone."**

**Gaps it leaves.** No handling of the departed-provider case, where the practice legitimately needs to complete enrollment paperwork for someone who has left, or of the practice owner who *is* the provider and reasonably wants to delegate broadly to a spouse/office manager. No model for an external credentialing service as a scoped participant, which is how many small practices actually operate.

---

## 3. Referral Administration

### 3.1 Multi-channel referral intake

**Verdict — differentiator, if scoped to fax.** A specialty practice's referral intake *is* a fax machine and an email inbox, and the practices that lose money lose it here. But "structured API events, secure messages, faxes, uploads, and staff entry" is five connectors; beta needs one and a half.

**Make it more useful.** Ship fax-to-referral and staff entry. Everything else is an upload. Give the practice a dedicated inbound fax number that lands as a queue item with a preview, page thumbnails, an OCR'd suggestion for patient/sender/service, and three buttons: match, new patient, not a referral. Target under 30 seconds per fax. Time-stamp arrival and start an aging clock immediately — before matching, before anything — because the referral SLA runs from receipt, not from processing.

**Alternate/better solutions.**
- *(Simpler)* **The fax inbox is the feature.** Don't build a referral object in beta. Build a shared, searchable, assignable inbound-document inbox with an age clock and a disposition. Most of the value of referral management for a small practice is "no fax sits for six days with nobody's name on it." The referral case can be added when the inbox proves insufficient.
- **QR/barcode cover sheets for known senders.** Give frequent referring offices a pre-printed cover sheet carrying a sender ID and, where lawful, a patient token. Inbound faxes then self-classify with high confidence, and the return status fax matches automatically. This is a two-week build that converts the worst channel into a semi-structured one.
- **Email-and-portal drop with a per-sender address.** `refer-drmiller@practice.example` — sender identity comes free from the address, no OCR needed for the hardest field.

**Reframe.** **"No inbound page waits longer than a day without a name and a next step on it."**

**Gaps it leaves.** Nothing on the *phone* referral, which is a large share of small-practice volume and produces no document at all. Nothing on the referral that arrives as a patient standing at the desk holding a piece of paper. No treatment of duplicate faxes (the same referral sent three times over four days is the single most common intake noise) or of the multi-patient fax batch.

### 3.2 Identity-safe matching

**Verdict — essential-core.** Duplicate patient records created at referral intake poison eligibility, statements, and the EHR link forever, and they are cheapest to prevent at exactly this moment. The stated stance — route ambiguity for adjudication, never auto-create on uncertainty — is correct.

**Make it more useful.** Bias hard toward "attach to existing" and make merge easy rather than making creation hard. Show the candidate matches with the *distinguishing* fields highlighted (DOB, last four of phone, address), not a similarity score — staff cannot calibrate a 0.87. Allow "I'm not sure" as a first-class disposition that parks the item rather than forcing a wrong choice. Make new-patient creation from a referral carry the referral's provenance so a later merge knows where the record came from.

**Alternate/better solutions.**
- *(Simpler)* **Match at scheduling, not at intake.** Let referrals sit unmatched and searchable; bind identity when the practice actually calls the patient to book, when a human is already on the phone confirming DOB. Removes an entire adjudication workflow and matches how staff behave.
- **Deferred-identity referrals.** Model the referral as attachable to *zero or one* patient with no penalty for zero, and make "referrals not yet linked to a patient" a normal, workable state rather than an exception queue. Most fax referrals are legitimately ambiguous for days.
- **Match against the EHR, not just openPractice.** If the connected EHR already has the patient, the authoritative match lives there; a lookup against the EHR's patient index at intake prevents the two systems diverging at record one.

**Reframe.** **"A referral is allowed to not know who it's about — but it is never allowed to guess."**

**Gaps it leaves.** No treatment of the pediatric/guardian case, name changes, or twins (which defeat DOB+last-name matching and are overrepresented in pediatric referral streams). No policy for a referral matched to the wrong patient and discovered after scheduling — the unmerge path.

### 3.3 Completion checklist

**Verdict — valuable-enhancer.** Useful, but the doc's own caveat is the whole point: "without treating all missing items as equal blockers." A checklist that reads as ten red X's on every new referral trains staff to ignore it within a week.

**Make it more useful.** Cap at five items, ordered by what blocks the *next* action rather than by domain. Show only the next one or two. Distinguish "can't call the patient yet," "can't book yet," "can't bill yet" — three gates, in order, each with its own tiny list. Auto-satisfy everything derivable (coverage from eligibility check, demographics from the fax) so the human list contains only human work.

**Alternate/better solutions.**
- *(Simpler)* **A single blocking reason.** One field: "what's stopping this?" chosen from a short list, set by whoever touched it last. Aggregating that field across referrals gives the practice better management information than a per-item checklist, at a fraction of the build.
- **Checklist derived from the offering.** The service catalog already knows what a colonoscopy referral needs (auth, prep instructions, prior records); generate the checklist from the offering rather than authoring per-referral rules.
- **Progressive gating by calendar distance.** Requirements tighten as the appointment approaches — nothing is required at receipt, coverage is required a week out, authorization two days out. A checklist with time in it is a workqueue; one without is a wall.

**Reframe.** **"What is the one thing stopping this referral from becoming an appointment?"**

**Gaps it leaves.** No handling of the referral that is administratively complete but clinically incomplete (the specialist won't see the patient without records the sender hasn't sent) — the boundary is philosophically right but operationally leaves staff with a green referral and an angry doctor. No SLA differentiation for urgent referrals asserted as urgent by the sender.

### 3.4 Referral-to-appointment conversion

**Verdict — essential-core.** This is where the cluster pays for itself: no re-entry from a fax into a scheduling screen, and the referral context (source, requested service, coverage, authorization) rides through to the claim, where the referring provider NPI is a required field that small practices constantly get wrong or omit.

**Make it more useful.** Make it one button on the referral: "Book." Open the scheduler pre-filtered by offering, provider readiness, and the patient's stated availability. Carry the **referring provider NPI** into the appointment and thence to the claim automatically — this single data path prevents a recurring rejection category and is worth building even if nothing else in this atomic ships. Emit the "referral scheduled" status event as a side effect of booking, never as a separate step someone must remember.

**Alternate/better solutions.**
- *(Simpler)* **A referral field on the appointment.** Skip conversion; let the scheduler attach an inbound document and referring provider to any appointment. 70% of the value, one field, no lifecycle.
- **Book-from-the-fax.** Put the scheduler inside the fax preview so staff never leave the document while calling the patient. Workflow, not data model — and it is what actually makes the phone call short.
- **Referral-generated self-scheduling link.** Text the patient a booking link constrained to the referred offering and ready providers. Converts the practice's highest-friction step (phone tag with a patient who doesn't answer) into an asynchronous one, and the constraint set comes free from the readiness graph.

**Reframe.** **"The fax becomes a booked, billable appointment without anyone retyping a single field."**

**Gaps it leaves.** No model for the referral that becomes a *series* of appointments (physical therapy, injections) with a visit count and an authorization unit count — the doc raises it as an open question and leaves it unanswered, but it is the normal case in several ambulatory specialties. No handling of a referral where the requested provider is not the available provider, which requires a sender notification.

### 3.5 Closed-loop status

**Verdict — differentiator.** Referring offices choose specialists partly on whether they ever hear back. A specialty practice that reliably confirms receipt and scheduling gains referral volume — this is a *marketing* feature disguised as an administrative one, and that framing should drive its design.

**Make it more useful.** Two automatic outbound events cover most of the value: **"received"** within an hour, and **"scheduled for <date>"** at booking. Send them the way the sender sent — fax in, fax out; portal in, portal out. Make the outbound artifact look like the letter a referring office expects (letterhead, patient name, DOB, date of service, your provider's name) rather than a system notification, because a human in the sender's office has to file it. Track "sender never responds" as a property of the sender, not a failure of the referral.

**Alternate/better solutions.**
- *(Simpler)* **Auto-fax receipt confirmation only.** One event, fully automatic, no configuration. It is the cheapest referral-relationship feature in existence and almost nobody does it.
- **A weekly per-sender digest.** Instead of per-referral status, fax or email each referring office a Monday summary of every patient they sent and where it stands. Fewer transmissions, more useful to the recipient, and it doubles as a relationship touchpoint.
- **A sender-facing status page.** A tokenized link (no login) per referral showing status. Works for the minority of senders who are digital, degrades to fax for the rest.

**Reframe.** **"Every office that sends us a patient hears back twice, automatically, in whatever format they can actually read."**

**Gaps it leaves.** The whole non-digital reality is acknowledged but not designed for: fax delivery is unacknowledged in practice (a transmission report is not a receipt), the recipient may be a shared fax machine, and there is no read confirmation. Nothing on minimum-necessary limits for outbound status content — a fax to a wrong number is a breach, and the outbound path needs a confirmed-fax-number registry per sender. Nothing on the closing event that matters clinically (the consult note going back), which is EHR-owned but which the *referring* office will blame the practice for.

### 3.6 Leakage and aging visibility

**Verdict — valuable-enhancer (aging yes, leakage later).** Aging is free once intake exists and is directly actionable. "Leakage" in the outbound sense is a health-system concept — a small independent practice does not have a network to leak from. Conversion and abandonment analytics are useful but are a month-two feature.

**Make it more useful.** Ship one number on the dashboard: **oldest unworked referral**, and one list: referrals over N days. Add per-sender volume and conversion rate later, because that is a business-development asset for the practice owner (which referring offices actually send us patients, and did Dr. Miller stop?) — frame it that way rather than as operations analytics.

**Alternate/better solutions.**
- *(Simpler)* **An age column and a default sort.** No analytics feature at all. Oldest first, forever.
- **Sender scorecard.** Flip the metric from internal efficiency to relationship intelligence: volume, trend, conversion, and average time-to-schedule per referring office, delivered monthly to the owner. Same data, far higher perceived value.
- **Reason-for-not-scheduled taxonomy.** A short forced-choice on closure ("couldn't reach patient," "patient declined," "wrong specialty," "insurance not accepted") generates the only analytics a small practice will act on.

**Reframe.** **"Which offices send us patients, and how many of them do we actually see?"**

**Gaps it leaves.** No measurement of the referrals the practice *turns away* for insurance reasons, which is exactly where the readiness graph and referral intake should meet ("we don't take that plan" should be answerable at intake, in seconds, and counted).

---

## 4. Administrative Documents And Claims Attachments

### 4.1 Administrative evidence object

**Verdict — essential-core.** Every other feat in this atomic and half the cluster depends on one governed representation of "a thing we received or produced." Getting the envelope right once is much cheaper than retrofitting provenance onto five domain-specific stores.

**Make it more useful.** The listed envelope has roughly twenty attributes; beta needs about eight to be *required* — purpose, source channel, received timestamp, subject reference, type, content hash, retention class, custodian — and the rest optional. Build on Frappe's File doctype with private storage rather than a parallel binary store; the differentiation is the metadata and the lifecycle, not the bytes. Critically: **the object must never be user-visible as an object.** Staff see "the patient's insurance card," "the signed consent," "the fax from Dr. Miller" — never an evidence record.

**Alternate/better solutions.**
- *(Simpler)* **Attachment plus purpose.** Extend Frappe attachments with three fields — purpose, source, and retain-until — and derive everything else. Ship the full envelope only for the classes that need it (signatures, attachments transmitted to payers, disclosures).
- **Two tiers, explicitly.** A *casual* tier (scanned card, insurance photo — low ceremony, no hash, short retention) and a *governed* tier (signed forms, transmitted attachments, disclosures — full envelope, hash-anchored, immutable). Applying one ceremony level to everything is why enterprise document layers are so slow to use.
- **Content-addressed storage.** Store by hash, dedupe automatically. The same fax arriving three times becomes one object with three receipt events — which solves 3.1's duplicate problem for free.

**Reframe.** **"Every file in the practice knows why it exists, where it came from, and when it may be destroyed."**

**Gaps it leaves.** Nothing on documents that *supersede* each other in dispute (see 4.4). Nothing on documents received before a patient exists, or attached to a person who later merges. No treatment of practice-level documents with no patient at all — contracts, W-9s, payer letters, the CLIA certificate — which is a large share of what a small practice files and which the envelope's patient-centric framing quietly assumes away.

### 4.2 Unmatched intake queue

**Verdict — essential-core.** This is the queue where a small practice's inbound reality actually lives, and it is the same surface as 3.1. Building it once for faxes, uploads, scans, and payer mail is the correct consolidation.

**Make it more useful.** One inbox for *everything* inbound, regardless of eventual domain, with disposition buttons that route to referrals, patient chart, payer correspondence, or trash. Age every item. Assign every item. Make "not sure" park it with a follow-up date rather than forcing a wrong route. Show OCR suggestions inline with the page image so the human verifies rather than re-reads.

**Alternate/better solutions.**
- *(Simpler)* **A shared email/fax mailbox with disposition tags.** Genuinely — for a 3-person practice, a well-organized mailbox with a naming rule beats a workflow engine. Build the workflow only where it beats a mailbox: aging, assignment, and auto-linking.
- **Match-on-arrival via outbound tokens.** Anything the practice sent out (status fax, forms packet, records request) goes out with a token; the response arrives pre-matched. Shrinks the unmatched queue by attacking its inputs rather than processing its outputs.
- **Bulk triage.** Multi-select and route in one action, because a Monday morning queue is 40 faxes and 30 of them are one of three types.

**Reframe.** **"One pile, one clock, one name on every page."**

**Gaps it leaves.** No handling of misdirected PHI — a fax for another practice's patient arrives regularly and has a specific disposal-and-notify obligation that a "trash" button does not satisfy. No path for a document that is *partially* relevant (a 40-page packet where three pages are the referral).

### 4.3 Destination-aware routing

**Verdict — differentiator.** The clinical/administrative boundary is openPractice's architectural thesis, and routing is where it becomes concrete and visible. Getting this right is what lets the product credibly claim it is not a shadow chart.

**Make it more useful.** Default every uncertain document to the EHR with a reference retained locally, rather than defaulting to retention. Make the routing decision a one-click, reversible action with an explanation of the consequence in plain language: "This goes to the chart in openChart. openPractice will keep a link, not a copy." Track and display "sent to EHR, not acknowledged" as work — a routed document that silently failed to land is worse than one that stayed.

**Alternate/better solutions.**
- *(Simpler)* **Route by document type, once.** A per-type routing table set at implementation (insurance card → keep; consult note → send; payer letter → keep) covers 90% of volume with zero per-document decisions.
- **Keep nothing clinical, ever — reference only.** A harder line: openPractice never stores clinical binaries, only a pointer plus hash. Slower for staff (they must open the EHR), dramatically smaller PHI surface, and unambiguous for the boundary claim. Worth prototyping as the default.
- **Hold-and-forward with expiry.** Retain a transient copy for N days to survive EHR outages, then auto-dispose once acknowledgment is confirmed. Practical without becoming a duplicate repository.

**Reframe.** **"Clinical paper passes through this practice; it doesn't live here."**

**Gaps it leaves.** No answer for the EHR that has no ingestion API, which is likely for a small practice's existing system — the fallback (staff manually upload to the EHR) needs to be a tracked, evidenced action, not an assumption. Nothing on documents that are legitimately both (a signed ABN, a DME order, an FMLA form) and must exist on both sides.

### 4.4 Versioned forms and signatures

**Verdict — essential-core.** A consent whose signed content version is unknown is not a consent. This is cheap to do right at the start and effectively impossible to reconstruct later, and it is one of the few places where a small practice's legal exposure is directly reduced by software.

**Make it more useful.** Bind the signature to a **hash of the exact rendered content in the exact language shown**, plus the signer's identity, the authority basis (self / parent / POA / guardian), method, and timestamp. Auto-generate a one-page "signature receipt" PDF containing the hash and the rendered form — this is the artifact that ends a dispute. When a form version changes, generate re-signature work only for patients whose next visit occurs after the change, not for the whole panel.

**Alternate/better solutions.**
- *(Simpler)* **Sign the rendered PDF, keep the PDF.** Skip content versioning as a concept: flatten what the patient saw into an immutable PDF at signature time and store that. Disputes are answered by producing the file. Vastly simpler than a version graph, and legally more direct.
- **Chained receipts.** Hash each signature receipt with a reference to the prior one, giving tamper-evidence without external anchoring or a blockchain conversation.
- **Signature as an event on the patient timeline.** Present it where staff look — "signed HIPAA NPP v3, Mar 4, on the kiosk, in Spanish" — rather than in a forms module.

**Reframe.** **"We can always show exactly what this person saw, in what language, and what they agreed to."**

**Gaps it leaves.** Revocation is listed but unmodeled: a revoked consent must propagate to communications, autopay, and disclosure decisions *immediately* and retroactively constrain what may be sent. No treatment of wet-signature-on-paper, later scanned — the dominant real-world path — where content version must be inferred from the printed form's footer. No handling of a representative whose authority ends (a minor turning 18, a POA revoked).

### 4.5 Claims-attachment workflow

**Verdict — defer the standards half; the manual half is essential and should be split out.** With the X12 275/277 v6020 rule's compliance horizon roughly two years past its 2026 effective date, no payer will accept an electronic attachment from a beta practice, and no clearinghouse contract will support it. Meanwhile, the *actual* small-practice problem — a payer asked for records and there is a 30-day clock — has no home in these docs at all.

**Make it more useful.** Split into two feats. Build **payer records-request tracking** now: a request object with payer, claim, what was asked for, deadline, owner, what was sent, how, and the acknowledgment or lack of one. Channel-agnostic — fax, portal upload, mail — because that is what beta will use. Keep the X12 packaging as a designed-for, unbuilt seam behind the same request object, so adopting 275 later is a transmission adapter rather than a redesign.

**Alternate/better solutions.**
- *(Simpler)* **A deadline and a folder.** Requests become tasks with a due date and an attachment set. The clock is the feature; the envelope is not.
- **Assemble-from-references.** Rather than storing clinical material, build the response package as a purpose-bounded, time-limited pull from the EHR at send time, retaining only the manifest and hash. Answers the doc's own open question in the direction that best protects the boundary.
- **Pre-emptive attachment.** For the payer/code combinations that always trigger a request, attach documentation with the original claim. Fewer requests beats faster responses, and the pattern is learnable from the practice's own denial history.

**Reframe.** **"When a payer asks for paperwork, the clock is visible, the package is assembled from what we already have, and we can prove what we sent."**

**Gaps it leaves.** Prior-authorization attachments are explicitly on a different standards track and a different (earlier, API-driven) timeline than claims attachments — a practice will hit auth attachments long before claim attachments, and this feat's framing points at the later one. Nothing on appeals packages, which reuse the same mechanics with different deadlines and much higher stakes for a small practice.

### 4.6 Retention and disposition controls

**Verdict — valuable-enhancer.** Retention classes and a disposition clock are cheap and prevent unbounded PHI accumulation. Legal hold, verified disposal with evidence, and configurable retention policy engines are enterprise compliance machinery a 3-person practice will never configure.

**Make it more useful.** Ship three retention classes with sane defaults (transient / standard / long) mapped from document type, one practice-level override, and a "nothing is ever auto-deleted without a 30-day review list" rule. Show the practice what is scheduled for disposal before it happens. Legal hold reduces to a single flag that stops disposal — that is 100% of what a small practice needs from it.

**Alternate/better solutions.**
- *(Simpler)* **Never delete; expire from view.** Move aged documents to a cold, access-logged state instead of destroying them. Removes the entire "verified disposal with evidence" problem while achieving the minimum-necessary access goal.
- **Retention derived from the financial clock.** Anchor disposition to the claim/appeal/audit lifecycle (last financial activity + N years) rather than to a document-type table, which is closer to how records obligations actually run and needs no configuration.
- **Export-then-purge.** Practice-controlled: produce a signed archive the practice keeps, then purge from the running system. Puts the retention decision where the liability sits.

**Reframe.** **"Nothing sensitive stays here longer than it needs to, and the practice sees the list before anything goes."**

**Gaps it leaves.** State retention minimums vary and can exceed federal ones; a single default set will be wrong somewhere and the doc has no jurisdiction dimension. Nothing about disposition of documents the practice never should have had (misdirected faxes). Nothing about what happens to evidence when the practice leaves openPractice — the exit path is a retention question and a trust question.

---

## 5. Implementation And Configuration Lifecycle

### 5.1 Guided practice bootstrap

**Verdict — essential-core.** Same feature as 1.1 seen from the process side; they should be one thing. Implementation burden is explicitly the competitive wedge against incumbents, so this is where the product's first impression is made or lost.

**Make it more useful.** Define "bootstrapped" by outcome, not coverage: the practice can book an appointment, verify eligibility, and produce one clean claim. Order the flow so that each step unlocks a visible capability, and let the practice *stop and use the system* at any point. Never show a progress bar to 100% — show "you can now book appointments," then "you can now bill Medicare."

**Alternate/better solutions.**
- *(Simpler)* **Ten questions, then work.** Ask only what blocks booking. Discover everything else lazily, at the moment of first use, with the blocking action named.
- **Setup as a guided conversation with an agent.** Genuinely reframable: the office manager describes the practice in prose and answers follow-ups; the system proposes a configuration and shows it back in plain English for confirmation. This is a real differentiator against incumbents' 40-screen wizards, and the confirmation step keeps a human authoritative.
- **Ship a working demo practice and let them edit it.** Nothing is more intimidating than an empty system. Start from a fully configured fictional practice with realistic offerings, then walk through replacing its facts with theirs. Editing beats authoring for non-technical users, every time.

**Reframe.** **"Answer enough questions to book Tuesday's patient; answer the rest when they get in your way."**

**Gaps it leaves.** No handling of the practice that is *already running* and switching mid-cycle — open A/R, in-flight claims, existing future appointments, patients mid-payment-plan. That is the real beta scenario and it is a different problem from bootstrapping a greenfield practice.

### 5.2 Versioned configuration set

**Verdict — questionable.** The need is real (unexplained drift, "who changed the fee schedule"), but packaging domain settings with approvals, dependencies, and compatibility metadata is an enterprise release-management construct for a system whose entire configuration will be changed by one person. It will be built, admired, and bypassed.

**Make it more useful.** Reduce to what the practice will actually consume: a **change log** ("what changed, when, by whom, and what it affected") and **undo**. Frappe already records field-level versions per document; the incremental work is a cross-document, human-readable feed and a revert action — days of work, not a subsystem. Add approval gates only for the handful of settings that move money (fee schedules, merchant destination, write-off authority, autopay rules).

**Alternate/better solutions.**
- *(Simpler)* **A practice changelog page.** One reverse-chronological feed of every configuration change in plain language, filterable, with revert. This is the entire feature for a 3-person practice.
- **Snapshot-and-restore.** Periodic full configuration snapshots (Frappe fixtures-style export) with named restore points. "Restore to before Tuesday" is more comprehensible than dependency-aware promotion, and much easier to trust.
- **Config-as-git for the technical minority.** Export the whole configuration set to a repo for practices or partners who want review workflows, without imposing it on anyone else. Serves the open-source constituency without taxing the beta customer.

**Reframe.** **"Anything anyone changed is visible in one list, and any of it can be put back."**

**Gaps it leaves.** No distinction between configuration that is *safe* to revert (a template, a reminder cadence) and configuration that is not (a fee schedule under which claims were already submitted). Revert without that distinction is a footgun.

### 5.3 Migration rehearsal

**Verdict — essential-core, if scoped brutally.** Data migration is where implementations die and where trust is won or lost. But "declared source exports, row-level provenance, reconciliation reports" needs a hard scope: for a small practice, migration is a demographics file, a future-appointments file, and an A/R aging report — and the A/R usually should not migrate at all.

**Make it more useful.** Support exactly three imports with named, published templates: patients, future appointments, and insurance coverage. Every import runs twice — a dry run producing an accept/reject report the practice reads *before* committing, then the commit. Show rejects with the source row and a plain-language reason, and let the practice fix and re-run only the rejects. Recommend explicitly that the practice **run off old A/R in the legacy system** rather than migrating balances; make that the documented default, with an opening-balance import as a governed exception.

**Alternate/better solutions.**
- *(Simpler)* **Frappe Data Import with better templates and copy.** The platform already does CSV import with per-row errors. Ship excellent templates, a pre-import validator, and a reconciliation summary — most of the "rehearsal" value with almost no new machinery.
- **Import into a sandbox site, then promote.** A cloned site for the dry run makes rehearsal literal and gives the practice a training environment for free.
- **Progressive migration.** Don't migrate patients at all; import a patient on first touch (lookup against a staged file when someone books or arrives). The practice's active panel migrates itself over 90 days and the inactive tail never has to. Radically less risk, and dramatically less garbage carried forward.

**Reframe.** **"Show me exactly what will and won't come across — before anything moves."**

**Gaps it leaves.** Patient identity merges across systems and duplicate detection at import are named as "governed review" but unspecified, and they are the hardest part. Nothing on document/chart migration, nothing on recurring appointment series, nothing on payment plans and stored payment methods (which cannot migrate and which the practice will assume do).

### 5.4 Policy simulation

**Verdict — defer.** A synthetic-scenario simulation harness for scheduling, readiness, communication, authorization, claim-edit, routing, and financial rules is a serious engineering investment aimed at a customer with roughly six rules. The genuine underlying need — "what will this change do?" — is better served by preview, not simulation.

**Make it more useful.** If anything ships, ship **change preview on real data**: "Applying this rule to last month's actual appointments would have blocked 4 bookings; here they are." Retrospective replay against real history is more convincing than synthetic scenarios and requires no scenario authoring.

**Alternate/better solutions.**
- *(Simpler)* **Dry-run mode per rule.** A rule can be active-but-logging-only for two weeks. The practice sees what it *would* have done. Zero simulation infrastructure.
- **Shadow evaluation with a weekly digest.** All new rules run in shadow by default and report; promotion to enforcing is one click after the practice sees the effect.
- **Ship the rules pre-validated instead.** The vendor tests the rule library; the practice tunes thresholds, not logic. Simulation is mostly a symptom of over-configurable rules.

**Reframe.** **"Turn it on quietly for two weeks and show me what it would have done."**

**Gaps it leaves.** Nothing addresses the actual small-practice fear, which is not "will the rule work" but "will this stop me from seeing patients tomorrow." Any preview needs a blast-radius-on-the-schedule view.

### 5.5 Cutover control room

**Verdict — questionable.** "Control room" is the wrong ceremony by two orders of magnitude. A 3-person practice goes live on a Monday after a light Friday; the prerequisites, owners, data freeze, and fallback fit on an index card. Building a cutover management surface signals that openPractice implementations are hard — the opposite of the intended positioning.

**Make it more useful.** Replace with a **go-live checklist** of at most 15 items, printable, with the fallback plan stated in one line per item ("if eligibility doesn't work: verify on the payer portal, keep seeing patients"). Add a "first week" companion: a daily one-screen check of what got stuck. The valuable half of this feat is stabilization, not cutover.

**Alternate/better solutions.**
- *(Simpler)* **No cutover at all — progressive activation.** Run scheduling in openPractice for two weeks while billing stays in the old system. Then eligibility. Then charges. Then claims. Each step is independently reversible, and there is never a day when the practice bets everything. This is both simpler *and* safer, and it should be the recommended path.
- **Parallel-run reconciliation.** For one billing cycle, run both systems and reconcile daily counts. The reconciliation report *is* the acceptance evidence, generated by operation rather than authored.
- **"First of each" milestones.** Acceptance is defined by first booked appointment, first eligibility check, first clean claim, first posted remittance, first patient payment, first reconciled deposit. Six events, each with a celebration and a check. Far more motivating than a cutover plan, and each one is a genuine acceptance test.

**Reframe.** **"There is no go-live. There is a first appointment, a first claim, and a first dollar — and each one turns green on its own."**

**Gaps it leaves.** Nothing on staff training or the fact that in a 3-person practice, go-live week means someone is doing double work while still answering the phone. Nothing on the vendor-side obligation during stabilization, which for a beta is the actual product.

### 5.6 Configuration promotion and rollback

**Verdict — defer.** Development, test, training, and production environments for a practice with four staff is fantasy infrastructure. The practice will have one site. Promotion pipelines solve a problem they do not have and add a concept they will never learn.

**Make it more useful.** Collapse into two capabilities the practice will use: a **sandbox copy** (one-click clone of the live site, refreshed on demand, for training and trying things) and **undo** from 5.2. Nothing promotes between them; the sandbox is for learning, and real changes are made live with a change log and a revert.

**Alternate/better solutions.**
- *(Simpler)* **Backup, change, restore.** Frappe already backs up and restores a site. "Take a snapshot before you change the fee schedule" is a workflow, not a subsystem.
- **Training mode inside production.** A flagged practice-mode where actions are recorded but produce no external effects (no claims, no texts, no charges), so training happens against real configuration without a second environment.
- **Promotion for the partner channel only.** If implementation partners or an MSO ever need it, promotion is *their* feature, packaged separately, not the practice's.

**Reframe.** **"One system, a safe place to practice, and the ability to put anything back."**

**Gaps it leaves.** Explicitly acknowledges that financial and transmitted-claim rollback requires compensating action rather than reversal, then offers no design for it — which is the only rollback question that actually matters. A configuration mistake that produced 40 wrong claims needs a claim-correction workflow, not a config revert.

---

## 6. Access Equity And Channel Parity

### 6.1 Structured access needs

**Verdict — essential-core.** Language and accommodation needs must be structured to be actionable (booking a longer slot, arranging an interpreter, choosing a form language), and re-asking a patient their language at every visit is both a service failure and a legal exposure. Small object, large effect.

**Make it more useful.** Keep it to five fields that change behavior: preferred spoken language, preferred written language, interpreter needed (y/n/type), format needs (large print, screen reader), and preferred contact channel including "no digital." Surface them where they change what staff *do* — on the appointment, on the check-in screen, on the outbound message composer — not in a demographics tab. Capture once, at registration, in one question.

**Alternate/better solutions.**
- *(Simpler)* **Two fields and a note.** Language and "needs help with." Everything else is free text until a partner proves otherwise. Over-structuring a sensitive attribute set is both a privacy risk and a data-quality one.
- **Derive from behavior, confirm with a human.** If a patient chose Spanish on the kiosk three times, propose setting the preference — never set it silently, never infer proficiency.
- **Needs attached to the task, not the person.** "This visit needs an interpreter" is often more accurate than "this person needs an interpreter," reduces the persistent-profile risk, and matches how accommodations are actually arranged.

**Reframe.** **"Ask once, remember, and never make the patient explain again."**

**Gaps it leaves.** Nothing on the family member who interprets (common, often inappropriate, and something the record should distinguish from a qualified interpreter). Nothing on a need that applies to the representative rather than the patient — the English-speaking patient with a Spanish-speaking guardian, or vice versa.

### 6.2 Channel capability matrix

**Verdict — questionable.** This is an excellent internal design artifact and a poor feature. Declaring per-channel capability and identity assurance in a configurable matrix invites a practice to configure something they have no basis to reason about, and the beta has approximately three channels.

**Make it more useful.** Make it a *published design contract* — a document and a test suite asserting that every patient task is completable by staff on the phone — rather than a runtime configuration surface. If it must exist in code, make it a fixed, vendor-owned table, not practice-editable.

**Alternate/better solutions.**
- *(Simpler)* **One rule: staff can do anything.** Assert as an invariant that every patient-facing task has a staff-executable path, and enforce it with tests. No matrix needed.
- **Capability declared by the task, not by a matrix.** Each task type declares its own channel support inline. Locality beats a central table nobody maintains.
- **Assurance tiers, not a matrix.** Three identity levels (anonymous link, verified patient, staff-verified in person/phone) attached to actions. Far fewer cells, same protection.

**Reframe.** **"Anything a patient can do on their phone, a staff member can do for them on the phone."**

**Gaps it leaves.** Nothing about channel *outage* — when the SMS vendor is down, which tasks silently stop, and who is told. That is the operational half of channel capability and it is the half that will actually bite.

### 6.3 Equivalent fallback

**Verdict — essential-core.** This is the real mechanism behind the whole atomic and the one thing that must exist: one task, one evidence trail, resumable across channels. Without it, staff assistance means re-entry, and every equity commitment becomes invisible labor.

**Make it more useful.** Implement as **assisted mode**: a staff member opens the exact same task the patient would see, with a banner recording that it is being completed on the patient's behalf, by whom, with what authority, and why. Same form, same validation, same evidence — one flag different. Support mid-task handoff both directions (patient starts on phone, finishes at the desk; staff starts, texts the rest to the patient) via a continuation link.

**Alternate/better solutions.**
- *(Simpler)* **Staff-view of every patient form.** No task framework: just ensure every patient-facing form has an authenticated staff route with an "on behalf of" attribution. One rendering, two entry points.
- **Print-and-scan parity.** Every digital form must produce a paper version whose scanned return populates the same fields (via a form ID and field anchors). Paper becomes a channel, not a dead end — and this is what actually happens in a small practice.
- **Read it to them.** A staff script view that renders the form as a phone-call script with the answer fields inline. Turns the fallback into a better tool than the primary path for the phone channel.

**Reframe.** **"Every patient task has a phone number, and using it costs the staff member nothing extra."**

**Gaps it leaves.** No treatment of signature in assisted mode — a staff member cannot sign for a patient, so the fallback has a hard boundary at consent that needs an explicit design (verbal consent with witness attestation, mailed form, sign-at-arrival). This is the most common real fallback failure and it is unaddressed.

### 6.4 Interpreter and accommodation work

**Verdict — valuable-enhancer.** Real need, real obligation, but for the beta it is mostly "flag it on the appointment and book a longer slot." A routing workflow with ownership and deadlines is more machinery than a 3-person practice arranging a phone interpreter needs.

**Make it more useful.** Two behaviors: an interpreter flag that automatically extends the appointment duration and appears on the day sheet, and a one-tap log of "interpreter used, vendor, duration" for evidence and cost tracking. That is the whole feature at beta scale.

**Alternate/better solutions.**
- *(Simpler)* **A flag and a longer slot.** The scheduling system already knows durations; accommodation is a duration modifier plus a visible note.
- **Phone-interpreter dial-out from the appointment.** One button that connects the practice to a telephonic interpreter line with the language pre-selected, logging the session. Converts an obligation into a convenience.
- **Pre-visit accommodation confirmation.** Include the accommodation in the appointment reminder ("an interpreter will be available") so failures surface before the patient arrives, not in the waiting room.

**Reframe.** **"When the patient arrives, the help they asked for is already arranged — and we can prove we arranged it."**

**Gaps it leaves.** Nothing on cost, which is the actual reason small practices skip interpreters. Nothing on a failed accommodation (interpreter unavailable at visit time) and its escalation — reschedule, proceed, or use a family member — which is a decision with legal weight and needs a recorded reason.

### 6.5 Authoritative translation control

**Verdict — essential-core (as a rule, not a module).** Cheap to enforce, expensive to retrofit, and the failure mode — a machine-translated consent presented as authoritative — is a real legal problem. Ties directly to 4.4: the signature must record the language of the rendered content.

**Make it more useful.** Enforce three rules and build almost nothing: (1) a form may be signed only in a language with an approved translation; (2) the signature record stores the language and content hash; (3) machine-translated text is visually and structurally marked as guidance and can never be a signable surface. Ship English plus the design partner's second language, and let unsupported languages route to assisted completion.

**Alternate/better solutions.**
- *(Simpler)* **Approved-languages allowlist per form.** One field. If the patient's language isn't on it, the task routes to staff. Done.
- **Translation as a versioned asset with its own review.** Each translation is a named artifact with a translator and an approval date, so adding a language is a content operation, not an engineering one — important for an open-source project where the community can contribute translations.
- **Sign in English with a witnessed verbal explanation.** Where no translation exists, an explicit, recorded fallback beats an unmarked machine translation and reflects real practice.

**Reframe.** **"Nobody signs something we translated with a robot."**

**Gaps it leaves.** No handling of translation drift — the English form is updated, the Spanish one isn't, and the system happily serves a stale translation. Version binding must be per-language with an explicit "translation out of date" state that blocks signing in that language.

### 6.6 Equity observability

**Verdict — defer.** Outcome analysis by access dimension is important and is also a small-cohort statistics problem in a practice with a few thousand patients, where any breakdown is either suppressed or re-identifying. The measurement infrastructure without a population to measure produces false confidence.

**Make it more useful.** In beta, capture the *dimensions* cleanly (language, channel used, assisted vs self-service, completion, abandonment) so the analysis is possible later, and report only aggregate operational facts: what fraction of tasks were completed with assistance, and where self-service abandonment is highest. Do not slice by protected attributes at this scale.

**Alternate/better solutions.**
- *(Simpler)* **Abandonment funnel by channel only.** No protected attributes at all. Where self-service breaks is a UX finding, not an equity report, and it is actionable now.
- **Aggregate across the deployment community.** For an open-source product, equity analysis is far more meaningful pooled across many practices with governed contribution — a genuinely differentiated open-metrics play, and safe at scale.
- **A parity assertion test suite.** Instead of measuring outcomes, prove capability: an automated test that every patient task is completable via every declared channel. Prevention rather than surveillance.

**Reframe.** **"Prove every path works, before measuring who used which one."**

**Gaps it leaves.** No stated guard against a practice using channel data to route or deprioritize, which is the actual risk the doc names but does not design against.

---

## 7. Synthesis: Small-Practice Administration

### 7.1 One administrative readiness graph

**Verdict — differentiator — and the product's spine.** This is the single idea most worth building, and it should be *the* organizing metaphor rather than one feat among forty. Every incumbent answers readiness questions late, in fragments, at the moment of failure. Answering them early, in one sentence, with an owner, is a defensible position.

**Make it more useful.** Unify 1.4, 2.5, and this into one evaluator with one output shape and one negative-form UI. Make the office manager's home page the graph's negative: everything not ready in the next 14 days. Precompute nightly, evaluate live at booking. Make "explain" mandatory on every verdict.

**Alternate/better solutions.**
- *(Simpler)* **The green board.** A single nightly job and one page. Ship it before any general-purpose evaluator exists.
- **Readiness as forward date ranges.** Store when each tuple becomes and stops being billable, so the system can answer "when can I book her" — a scheduling superpower nobody offers.
- **Readiness as a public API.** Expose the verdict as a documented, versioned endpoint so the connected EHR's scheduler, a website booking widget, or a call-center tool can consume it. Consistent with the packet's provenance-as-API differentiation.

**Reframe.** **"One question, asked constantly: is everything true that needs to be true for this to be seen and paid?"**

**Gaps it leaves.** Substitute and supervising arrangements, retroactive enrollment effective dates, and mid-year provider changes all break a naive readiness verdict. Without those, the graph is confidently wrong in exactly the situations where a small practice most needs it.

### 7.2 Referral-to-ready appointment

**Verdict — differentiator.** The end-to-end path — fax arrives, patient matched, coverage checked, readiness verified, appointment booked, sender notified — with no re-entry is a demo that sells the product in ninety seconds and a workflow no incumbent delivers coherently at this price point.

**Make it more useful.** Build it as a single vertical slice and use it as the beta's acceptance test. Instrument the elapsed time from fax receipt to booked appointment and publish it as the practice's headline metric. Everything else in this cluster should be justified by whether it shortens that path.

**Alternate/better solutions.**
- *(Simpler)* **Fax → appointment with an attached document.** Skip the referral case entirely; the fax rides on the appointment as evidence and the referring NPI flows to the claim.
- **Referral-triggered patient self-scheduling.** The practice texts a constrained booking link; the patient books themselves. Removes the phone-tag step, which is the longest segment of the path.
- **Two-minute triage.** A single screen combining fax preview, patient match, eligibility result, and open slots, with one confirm. Design the workflow first and derive the objects from it, not the reverse.

**Reframe.** **"From the fax landing to a booked, billable appointment — one screen, two minutes, zero retyping."**

**Gaps it leaves.** The unhappy paths dominate real volume and are undesigned: patient never answers, insurance not accepted, wrong specialty, sender sent nothing usable, patient wants a different provider or location. Each needs a fast, recorded disposition or the queue silently accumulates.

### 7.3 Configuration as operational evidence

**Verdict — valuable-enhancer.** Being able to prove which policy version produced a decision is genuinely differentiated and aligns with the packet's provenance thesis, but it is an audit-time and dispute-time value, not a daily one. Beta needs the *capture*, not the presentation layer.

**Make it more useful.** Stamp decisions with the version of the rule that produced them at write time — cheap, and it makes the evidence exist. Build the retrieval and evidence-pack presentation later, when someone actually asks.

**Alternate/better solutions.**
- *(Simpler)* **Decision stamps only.** A rule-version field on every automated decision. Retrieval is a query when needed.
- **Explain-this on any record.** Surface it as a user-facing affordance rather than an audit artifact: click any automated outcome and see why. Same data, daily value, and it dramatically cuts support burden.
- **Signed daily configuration digest.** One hash per day covering the practice's whole configuration state, cheaply anchoring "what the system was" without per-decision plumbing.

**Reframe.** **"Every automated outcome can explain itself, in the words of the rule that caused it."**

**Gaps it leaves.** No treatment of decisions made by *humans* overriding rules, which is where disputes actually concentrate. The override reason is more valuable evidence than the rule version.

### 7.4 Small-team implementation control

**Verdict — valuable-enhancer.** The goal is right — visible roles, approvals, reconciliation, and rollback without dedicated IT — but for 2-4 people, "roles and approvals" is largely a fiction: the office manager will hold every role, and a self-approval workflow is ceremony with no separation of duties behind it.

**Make it more useful.** Be honest about the staffing reality. Instead of approval workflows, use **friction plus receipts**: consequential actions require a typed reason and generate a notification to the practice owner. Retrospective review by the owner replaces prospective approval by a nonexistent second person. Keep only two roles in beta (staff, administrator) plus a provider identity.

**Alternate/better solutions.**
- *(Simpler)* **Everything is logged, the owner gets a weekly digest.** No approvals, full visibility.
- **Vendor-as-second-pair-of-eyes.** During beta, consequential configuration changes optionally notify the openPractice team — which is what a beta partnership actually is, and it beats simulating segregation of duties.
- **Time-delayed dangerous actions.** Actions like changing the merchant destination take effect in 24 hours with a cancel window. Protection without a second human.

**Reframe.** **"There is no second person to approve it — so make it hard to do by accident, and impossible to do invisibly."**

**Gaps it leaves.** Nothing on the practice with no administrator available (the office manager is on vacation and something expires). Nothing on vendor/partner access boundaries during support, which is a real HIPAA surface for a hosted beta.

### 7.5 Equal outcome across channels

**Verdict — essential-core.** As a *product invariant* this is right and should be enforced by tests, not by a feature. It is the correct summary of atomic 6 and its value is in being non-negotiable rather than in being built.

**Make it more useful.** Write it as an acceptance criterion applied to every patient-facing task in the beta, with an automated check in CI: no task ships without an assisted path. Publish it as a stated product principle — it is a real differentiator against app-first competitors and it costs almost nothing to hold if held from the start.

**Alternate/better solutions.**
- *(Simpler)* **A one-line rule in the definition of done.** "Staff can complete this on the phone." Enforced in review.
- **Assisted-first design.** Build the staff path first and derive the patient self-service path from it. Guarantees parity by construction and matches the beta reality where staff do most things anyway.
- **Publish a parity report per release.** Every task × every channel, generated from tests. Turns an invariant into marketing.

**Reframe.** **"Self-service is a convenience we offer, never a condition we impose."**

**Gaps it leaves.** Identity assurance genuinely differs by channel and some actions (changing where money goes, changing a contact of record) should not be equally available everywhere. Blanket parity needs a small, explicit exception list or it becomes a security hole.

---

## Missing feats

- **Retro-Enrollment Claim Hold And Release** — automatically hold (not reject, not submit) claims for any provider/payer/location pair that is not yet approved, and when approval arrives with an effective date, release every held claim on or after that date in one action while flagging those that fall before it for self-pay, appeal, or write-off. This is the highest-dollar administrative feature for a small practice and it is absent from every doc in the cluster.
- **Substitute And Supervising Billing Arrangements** — model locum tenens / fee-for-time (modifier Q6, with its continuous-day limit and a countdown), reciprocal billing (Q5), incident-to, split/shared, and supervising-physician arrangements for NPs and PAs, so the readiness graph can answer "billable *under whom*, with what modifier, until when" instead of a wrong red verdict.
- **Provider Onboarding And Departure Runway** — a backwards-planned countdown from a hire date to a first-billable date across NPPES, CAQH, and each payer application, telling the practice when it may safely start booking insured patients; and its mirror, a departure checklist covering payer terminations, NPPES, directory removal, panel reassignment, open referrals, in-flight claims, and records custodianship.
- **Price-By-Date-Of-Service** — resolve self-pay prices, offering prices, and Good Faith Estimate amounts by service date so a mid-cycle price change never reprices a prior estimate, and any estimate that becomes stale relative to a new price surfaces as work before the patient is billed.
- **NPPES Sync And Drift Watch** — poll the public NPI registry for the practice's org and provider NPIs, diff against locally held facts, and raise plain-language work when they diverge ("NPPES still lists the old suite number; three payers pull from NPPES").
- **Payer Records-Request Tracker** — a channel-agnostic object for "a payer asked us for documentation," carrying the deadline, the claim, what was requested, what was sent, how, and the acknowledgment — usable today over fax and portal, and the natural home for X12 275 when the standard becomes real.
- **Confirmed Fax Destination Registry** — a per-referring-office record of verified fax numbers, contacts, and preferred format, so outbound status and records never go to an unverified number; doubles as the referral-source master record for sender scorecards.
- **Location Opening Kit** — a guided checklist for adding a site: NPPES address, payer location adds, place-of-service defaults, schedule templates, statement and estimate addresses, merchant destination, and a computed "this location is billable on ___" date.
- **Assisted Mode** — a single, universal, one-keystroke "I am doing this for the patient" state available from any patient-facing task, recording actor, authority, and reason, so channel parity is one mechanism rather than a per-feature obligation.
- **Practice Change Log With Undo** — one reverse-chronological, human-readable feed of every configuration change with a revert action, replacing versioned configuration sets, promotion, and rollback for the beta scale.
- **Scheduled Changes Calendar** — a single page listing every future-dated change (price, provider start, enrollment effective date, fee schedule, form version) with owner and cancel, making effective dating visible as upcoming events rather than as a modeling concept.
- **Misdirected PHI Disposition** — a first-class disposition for inbound documents that are not ours, with the notify-and-destroy handling that "trash" does not provide.

## Cross-feat gaps

- **Enrollment ↔ claims has no valve.** Readiness produces a verdict; nothing consumes it to *hold* a claim. Between "we booked anyway" and "the payer denied it 45 days later" there must be a hold bucket with an owner and a release trigger. This is the single most important seam in the cluster and no doc owns it.
- **Structure ↔ pricing is silent on dates.** The service catalog owns prices; the payer-contract atomic owns contracted rates; neither states who resolves the price for a service date, and estimates and Good Faith Estimates depend on the answer. A mid-cycle price change currently has undefined behavior.
- **Referral ↔ readiness never meet at intake.** "Do we take this insurance for this service with this provider?" should be answerable in the first thirty seconds of a fax landing — it is the fastest possible referral disposition and prevents weeks of wasted work. Nothing connects the two.
- **Referral ↔ claim loses the referring provider.** The referring provider NPI is a claim requirement for a large share of specialty visits and a common rejection cause; the referral atomic captures the sender and the claims domain needs the NPI, but no doc states that this field flows automatically.
- **Documents ↔ signature ↔ consent revocation is one-directional.** Signatures are captured with versions; revocation is listed once and never propagated to communications, autopay, or disclosure decisions. A revoked authorization must retroactively constrain behavior, and nothing describes that path.
- **Provider departure has no owner anywhere.** Structure covers affiliation end-dating, enrollment covers termination, referrals hold open cases, documents hold custodianship, scheduling holds future appointments — and no feat assembles them. A mid-year departure is the most common structural event in a small practice and it currently fragments across five docs.
- **Implementation ↔ operations pretends go-live ends.** Configuration failures become "normal accountable work" per the synthesis, but nothing defines who watches them after the vendor's attention moves on. For a practice with no IT, an unworked configuration queue is indistinguishable from a broken system.
- **Access needs ↔ signature has a hard wall.** Assisted completion works for every task except the one that requires the patient's own signature, and no doc designs the paper, verbal, or deferred path — so the equity commitment silently fails at consent, which is where it matters most.
- **Evidence ↔ non-patient documents.** The evidence envelope is patient-centric, yet a large share of what a small practice files (payer contracts, W-9s, CLIA certificates, credentialing packets, roster letters) has no patient at all and no described home.

## Intuitiveness assessment

**Can the office manager set up and run this without consultants?** Not as currently framed — not because the features are wrong, but because the *vocabulary* is enterprise master-data management and it will leak into the interface. "Effective-dated affiliation," "practice configuration set," "administrative evidence object," "channel capability matrix," "readiness evaluation," and "migration run" are all correct engineering concepts and all fatal as user-facing nouns. The cluster currently describes six domains, roughly a dozen new object types, and at least four lifecycles for a customer who has never configured software more complex than a payroll portal.

She *can* run it if three things hold. First, setup is confirmation rather than authorship — the system reads the practice's own paperwork (W-9, a prior claim, an NPI lookup, a remittance) and asks her to say yes. Second, effective dating is invisible: she schedules changes and time-travels, and never sees a date-range grid. Third, everything she must act on arrives on one page, sorted by consequence, in sentences with her name or a colleague's name attached.

**The one mental model:** *the practice makes promises with dates on them, and openPractice tells you when a promise won't hold.* A provider promises to be billable to BCBS on April 12. A location promises to be open Tuesdays. A price promises to be $180 until June. A referral promises the sender an answer within a day. A consent promises the patient we won't text after they revoked. Every object in practice administration is a dated promise, and the entire interface reduces to two views: **what is true today** and **what breaks soon, and whose problem it is.** If the office manager learns only that, she can operate the whole cluster.

## Top 3 reframings

1. **"Is tomorrow's schedule billable?"** — Replace practice structure, provider readiness, credential tracking, and enrollment with one continuously answered question, evaluated per appointment per payer per date, expressed in negative form on the home page with an owner on every red line. Every feature in the cluster earns its place by turning a red line green or by keeping a green line green; anything that does neither is deferred.
2. **"You never edit; you schedule a change — and you can look at any day."** — Effective dating stops being a data model and becomes two affordances: *starting on ___* and *as of ___*, plus a calendar of upcoming changes with cancel. This one reframe removes the cluster's single largest usability risk and simplifies the schema at the same time, because history becomes an append-only change stream plus context frozen onto financial records.
3. **"There is no implementation project; there is a first one of each."** — Delete cutover, promotion, environments, and simulation. Setup is a conversation seeded by the practice's own paperwork; go-live is progressive activation (schedule first, then eligibility, then charges, then claims), and acceptance is six "firsts" that each turn green on their own. Implementation ceases to be a phase with a control room and becomes a property of a system that is safe to change while running.
