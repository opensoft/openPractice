# Feat Review Synthesis: Whole-System Findings

Status: brainstorm
Kind: review
Summary: Across 167 reviewed feats the packet is 42% essential-core and structurally sound but consistently modeled one or two organizational sizes too large — enterprise governance surfaces for a 2-4 person office, differentiators built as audit trails instead of work generators, no day boundary, no phone or fax, a dozen computed verdicts with nothing consuming them, and a headline revenue-integrity differentiator that appears in no release slice — all of which resolves into one composable mental model (the day, the thread, readiness, promises, money lines, the roster) and roughly fifty deduplicated missing feats, five of them beta-blocking.
Topics: openpractice-feat-review, synthesis
Captured: 2026-08-26

## How to read this

Six cluster reviewers independently graded 167 feats against the small-ambulatory-practice archetype. This document does not re-grade them. It does four things the individual reviews could not: it counts the whole, it names the patterns that only appear when six reviews are read together, it composes the six cluster-level mental models into one, and it adjudicates where reviewers disagreed.

Where this document contradicts a cluster review, the contradiction is deliberate and the reasoning is stated. Where three or more reviewers independently converged on the same conclusion without coordination, that convergence is flagged and treated as strong evidence.

---

# 1. Verdict rollup

| Cluster | Feats | essential-core | differentiator | valuable-enhancer | questionable | defer |
|---|---:|---:|---:|---:|---:|---:|
| Front office and access | 53 | 17 | 7 | 14 | 3 | 12 |
| Revenue cycle | 28 | 15 | 4 | 6 | 2 | 1 |
| Operations and intelligence | 16 | 6 | 3 | 3 | 1 | 3 |
| Platform and trust | 18 | 7 | 5 | 2 | 2 | 2 |
| Practice administration | 40 | 18 | 7 | 7 | 3 | 5 |
| Revenue integrity and product shape | 12 | 7 | 1 | 1 | 2 | 1 |
| **Total** | **167** | **70** | **27** | **33** | **13** | **24** |
| Share | | 41.9% | 16.2% | 19.8% | 7.8% | 14.4% |

Front-office counts include the seven synthesis compositions (essential-core 2, differentiator 2, valuable-enhancer 2, defer 1). Where a reviewer split a verdict ("essential-core mechanism, questionable as presented"), the feat is counted under the verdict assigned to the feat *as written*, which is what the reviewer instructed.

## What the distribution says

**The packet is not over-scoped; it is mis-shaped.** Only 37 feats of 167 (22%) drew a questionable or defer verdict, and 70 drew essential-core. That is not a bloated catalog. Cutting 22% would leave the beta essentially unchanged in size and would not fix anything the reviewers actually complained about. The dominant critique in every single review is not "too many features" but "right capability, wrong altitude, wrong surface, wrong vocabulary." Six reviewers used almost the same sentence: the modeling is sound and the product vocabulary does not exist yet.

**The defers cluster hard, and they cluster where the research went deepest.** Twelve of the twenty-four defers (50%) come from one cluster, and within that cluster eleven of twelve come from three atomic documents — the companion app, AI intake, and arrival sensing. Those three documents contain 22 feats, of which 11 defer and 2 are questionable: a 59% non-beta rate against a packet-wide rate of 22%. The packet spent three atomic documents and 13% of its total feat count on capabilities that will not ship, while the phone — the dominant channel — got zero feats and the fax got a fraction of one. This is the packet's single clearest attention-versus-value inversion and it is quantifiable.

**Revenue cycle is the healthiest cluster and the most complete thinking.** Fifteen essential-core out of 28 with one defer means the reviewer found almost nothing to cut — the risk there is entirely about *shape* (an API versus a worklist) and about twelve unowned seams, not about scope.

**The two clusters added most recently — practice administration and revenue integrity — carry the highest questionable counts relative to size** (3/40 and 2/12) and, critically, the most severe sequencing problem: revenue integrity's headline capabilities map to no release slice at all. New material was written at synthesis altitude without being pushed through the sequencing plan.

**Differentiators are real but under-converted.** Twenty-seven differentiator verdicts is a strong number for an open-source product competing against athenahealth and Tebra, and they concentrate in exactly the places the strategy claims: readiness (5 feats across two clusters), provenance and correlation (5), evidence-as-byproduct (3), denial and variance work (3), referral closed-loop (2), recovery verification (1). But nearly every differentiator verdict carries the same caveat in its justification — *this is only differentiating if it emits an artifact somebody touches*. The distribution says the differentiation is architecturally present and productively unrealized.

---

# 2. Cross-cutting diagnoses

Seven hypotheses were offered for verification. All seven verify; five need material extension. Six additional patterns appear in three or more clusters and were not on the list.

## a. Enterprise machinery for a 2-4 person customer — **VERIFIED, and stronger than stated**

Every cluster carries it, and the reviewers named the same artifacts independently:

- **Operations** — capability-aware routing, acceptance/completion SLAs, escalation ladders, delegation records with substitute authority, role-scoped team views with subscriptions and row-level sharing. The reviewer's summary: a three-person practice has *no routing problem, no escalation problem, no team-sharing problem, and no metric-governance problem*.
- **Platform** — a six-dimensional permission matrix editor, access-review campaigns, accounting of disclosures, an interface operations console with dead letters and replay eligibility, a public API program with deprecation windows and quotas, and revocable capability-scope negotiation for a sibling product that does not exist yet.
- **Practice administration** — versioned configuration sets with approvals and compatibility metadata, dev/test/training/production promotion pipelines, a policy-simulation harness, a cutover control room, and a channel capability matrix. Four of the five drew questionable or defer.
- **Revenue integrity** — contract-management modeling (fee schedules, case rates, carve-outs, precedence hierarchies, approvals) and a period-close ceremony, for a customer whose close happens in QuickBooks and who has at least one payer whose fee schedule they have never seen.
- **Revenue cycle** — the reviewer counted *six separate configuration screens* across four atomic documents (freshness windows, edit-rule authoring, cadence design, approval tiers, service-type code selection, auto-post thresholds), each individually defensible and collectively fatal.
- **Front office** — a booking constraint language, linked sequential holds across steps and resources, cohort audience tooling with approval workflow, and an app permission ladder.

**Extension 1 — the coverage is inverted against real risk, not merely oversized.** The platform reviewer states it most sharply: the items specified in full detail are low-yield at this size, while the events that actually cause loss are absent or reduced to a clause. Same-day offboarding, patient right of access, missing BAAs, credential expiry, ransomware recovery, and upgrade safety are the actual frequency-ordered failure list, and none of them has a feat. The same inversion appears elsewhere: the packet models a routing engine but not a phone call; a delegation subsystem but not an unplanned sick day; a cutover control room but not the fact that go-live week means one person doing double work while answering the phone; a contract-precedence hierarchy but not a merchant fee.

**Extension 2 — the enterprise smell has a single structural source: configuration.** Nearly every questionable verdict traces to the same thing — a surface that asks the practice to author a policy it has never had to articulate. The reviewers' convergent replacement is also uniform: ship opinionated defaults, hide the dial, and derive the policy from a fact the system already holds (the appointment date, the filing limit, the auth expiry, the statement cycle, the staff roster, the last claim). "Every security dial not shipped is a question she cannot get wrong" is the platform reviewer's version; "deadlines derived from facts, never from policy" is operations'; "setup is confirmation, never authorship" is practice administration's. These are the same sentence.

## b. Differentiators as audit surfaces rather than work generators — **VERIFIED, and it is the packet's central execution risk**

- Revenue cycle on SY-1: "the risk is that it ships as an API nobody in a three-person practice will ever call, which would make it a research asset rather than a product feature." Its provenance-twist test is applied to all 28 feats and returns the same verdict repeatedly: *auditor value, not biller value*.
- Operations on provenance: build it, and keep it **invisible** — "the practice does not want a provenance viewer; they want the answer to 'why does this say that' in one click, roughly twice a year."
- Platform on evidence packs: "evidence that is generated but never opened, formatted for a machine, or signed with a key no third party can validate is indistinguishable from no evidence at all."
- Practice administration on configuration-as-evidence: "beta needs the *capture*, not the presentation layer."
- Revenue integrity on variance: the output must be "a short list of payer behaviors, not a queue of claim lines."

**Extension — the reviewers independently named the same generated artifacts, which means the list is not speculative.** Across four clusters, the artifacts that justify the chain are: the **one-click appeal packet** (eligibility + auth + charge + claim as submitted + acknowledgment + remittance + letter), the **auto-derived secondary claim** from the posted primary remit, the **retroactive-repricing rebill list** with a dollar total, the **stuck-money worklist** (position + age + amount + owner), the **preventable-denial count** with dollars, the **backwards leakage sweep** ("every completed visit with no charge, no claim, or no payment"), and — the one nobody expected — **undo**. Operations makes the sharpest case: sell the chain as the undo story, because "a chain built for undo must be complete in a way a chain built for audit need not be," and staff feel undo weekly and audit annually.

**Second extension — provenance is currently specified three times and will be built twice.** Revenue cycle's financial thread, platform's end-to-end transaction trace (feat 14), platform's correlation-as-first-class-data (feat 17), and the AI per-interaction decision record (front office F8) are four descriptions of one object. The platform reviewer flags 14/17 as a self-duplicate and adds: "overlaps unreconciled with revenue-cycle provenance claims in the sibling cluster; one of the two must be authoritative or both will be half-built." That is the correct diagnosis and it is unresolved.

## c. Missing day-boundary rituals — **VERIFIED, and it is the highest-convergence finding in the entire review set**

Four reviewers, working independently on different clusters, each identified a day ritual as their cluster's largest omission or best intervention:

- **Operations**: End-of-Day Close is "the single largest omission in the cluster — it describes continuous queues, but a practice runs on days, and the day boundary is where money and data quality are actually lost." Plus the Morning Huddle Page, plus reframe #2, which is entirely this.
- **Revenue cycle**: the daily "close the day" ritual is "the single best intuitiveness intervention in the cluster" — a day is not finished until every arrived patient has a billing disposition.
- **Revenue integrity**: the Daily Balanced Day replaces the monthly close as the practice's actual control point, and "the books are always closed; month-end is just printing."
- **Front office**: end-of-day cleanup is named as one of the three moments where cognitive load concentrates, and "the packet emits work into many queues and never says how the day is closed."

**Extension 1 — it is not one boundary, it is a rhythm hierarchy, and five of six clusters need it.** Reading the reviews together yields a complete cadence the packet does not have: **day** (huddle open, close shut), **week** (Friday's next-week authorizations and unconfirmed appointments; the payer drift digest; the owner's five-minute money page), **month** (bookkeeper package, owner one-pager, statement cycle), **quarter** (evidence binder, access review, recovery drill), **year** (risk analysis, contract renewal, code-set update, revalidation). Operations proposes a Recurring Practice Calendar shipped pre-populated so the practice *inherits an operating rhythm instead of being asked to invent one*; platform proposes a compliance calendar with the same shape; practice administration proposes a scheduled-changes calendar. These are one object.

**Extension 2 — print is a first-class medium and the packet has no concept of it.** Four reviewers independently designed for paper: the printable huddle page read aloud in four minutes, the printed one-page tomorrow's-exceptions list ("a small practice's morning huddle happens around a piece of paper"), the quarterly evidence binder on a shelf next to the OSHA and CLIA paperwork, the index-card go-live checklist, the signed migration reconciliation sheet, the deposit slip the product generates. Print-first is a design constraint that materially improves the digital version and it appears in zero brainstorm documents.

## d. Missing real-world money paths — **VERIFIED, and the list is roughly triple the length hypothesized**

All six named paths verify with explicit reviewer support: paper EOBs and checks (revenue cycle missing feat; revenue integrity F9 gap), PLB and recoupments (revenue cycle missing feat; revenue integrity names the PLB Ledger as its first missing feat), merchant fees (revenue integrity missing feat; "card processing is a 2-3% tax on every patient dollar and the packet mentions fees exactly twice without giving them a destination"), self-pay and GFE (three clusters), secondary claims (revenue cycle: "15-25% of claim volume, is the packet's single best provenance application, and is currently named nowhere"), corrected claims and voids (revenue cycle: "one of the top three things a biller does after a denial," owned by no feat).

**Extension — nine further money paths are unowned and each one independently breaks the ledger:**

1. **Workers' compensation, auto, and liability** — no eligibility transaction, a claim number/adjuster/date-of-injury party model, jurisdictional fee schedules, different statement rules. A single such patient breaks eligibility, COB, estimates, and statements simultaneously.
2. **Non-visit revenue** — chronic care management, remote monitoring, e-visits, prolonged services. Every derivation feat in the packet assumes a visit exists.
3. **Payer-pays-patient on out-of-network claims** — service rendered, check mailed to someone else, full-charge patient balance.
4. **Virtual credit card payer payments** — a 2-3% payer-imposed haircut, growing, entirely absent.
5. **Patient financing settlements** (CareCredit/Cherry-class) — full-balance settlement net of a discount, with recourse and refund behavior.
6. **Interest payments (PLB L6)** — arrives as unexplained extra money, reads as an overpayment, and is other income for tax purposes.
7. **Cash drawer** — opening float, over/short, who counted it. The small practice's most common integrity failure, with no representation.
8. **Non-patient money** — rebates, records fees, settlement checks, returned refund checks. With patient obligation as the only entry point, all of these become permanent unapplied cash.
9. **Opening balances and the migration boundary** — the first beta customer arrives with 90 days of open AR and in-flight claims on day one.

Add **provider-enrollment gating of charges** (hold rather than bill for a provider not yet effective) and the count of unowned money paths reaches fifteen.

## e. Verdicts without valves — **VERIFIED, and it generalizes far beyond readiness**

Practice administration states it best: "Enrollment ↔ claims has no valve. Readiness produces a verdict; nothing consumes it to *hold* a claim. Between 'we booked anyway' and 'the payer denied it 45 days later' there must be a hold bucket with an owner and a release trigger. This is the single most important seam in the cluster and no doc owns it."

**Extension — the pattern is systemic. Twelve computed assertions in this packet have no consumer:**

| Assertion produced | Consumer that does not exist |
|---|---|
| Enrollment/participation readiness | A claim hold with retro-release |
| Previsit readiness | A wave-through at the desk (front office: "a fully prepared patient still queues") |
| Appointment readiness | A gate on the vacancy-recovery offer (front office: recovery "will systematically generate denials") |
| Coverage change detected | Invalidation of the estimates, statements, and claim-ready charges built on the old belief |
| Authorization approved | Unit decrement from posted charges; reservation at booking |
| Override recorded with a reason | Its eventual adjudication outcome, so the rule can score itself |
| Open variance / open appeal | A hold on the patient statement |
| BAA on file | Connector activation (platform: "nothing prevents an unpapered PHI flow") |
| Detector emitting events | Detector-health monitoring (a silent failure produces an empty queue *and* a healthy metric) |
| Consent revoked | Autopay, cadences, and disclosure decisions, retroactively |
| Expected allowable | The patient estimate and the desk conversation |
| Metric value | The queue it should agree with (operations: "denial rate reports 12% while the denial queue holds four items") |

**Second extension — the inverse defect also exists: gates without escapes.** The human-approval gate has no deadline-aware release, which puts it in direct conflict with timely filing (see §6). The authorization gate has no path for "we did it anyway because the patient needed it." The append-only ledger has no path for a required deletion. A verdict with no valve leaks; a gate with no escape blocks. Both are the same design omission — the packet models the decision and not what happens next.

## f. Architecture vocabulary leaking into the UI — **VERIFIED in all six clusters, with the same proposed test**

Named offenders, verbatim: *readiness contract*, *communication decision record*, *evidence-shaped arrival state*, *previsit completion percentage*, *effective-dated affiliation*, *practice configuration set*, *administrative evidence object*, *channel capability matrix*, *readiness evaluation*, *migration run*, *minimum-necessary authority designer*, *interface operations console*, *accounting of disclosures*, *dead letters*, *replay eligibility*, *access-review campaigns*, *capability matching*, *acceptance window*, *escalation ladder*, *substitute authority*, *workgroup subscription*, *semantic registry*, *unapplied cash*, *period close*, *PLB WO*, *999*, *277CA*, *CARC/RARC*, *837 loops*.

**Extension 1 — two reviewers independently proposed the same shippable acceptance test.** Front office: "can the newest person at the desk explain it to a patient in one sentence without using a product noun?" Platform: "if a screen's name would not appear in a sentence the office manager says out loud, she will not open it." That is not a critique; it is a definition-of-done line that belongs in the feature contract.

**Extension 2 — the leak is structural, not only lexical.** Front office: "seven documents read as seven product lines... the packet has reproduced the vendor org chart in its own document structure." Document structure becomes navigation structure becomes the user's mental model. Fifteen atomic documents will become fifteen navigation entries unless the surface model is decided independently of the document model — which is what §3 does.

## g. The dominant channels are invisible — **VERIFIED, and quantifiable**

**Phone.** Named as missing in three clusters. Front office: "the phone is the dominant channel for a small practice and is invisible across the entire cluster," plus B6's gap ("telephone metrics — hold time, abandoned calls, time to book — are the front office's actual bottleneck and are absent from every feat") and C6's gap ("attempts made by phone never enter the ledger at all, which makes every accountability number wrong"). Operations: "no path for work that arrives by phone — the largest single source in a small office — so staff will keep a parallel paper list and the inbox will be structurally incomplete from week one." Practice administration: "nothing on the phone referral, which is a large share of small-practice volume and produces no document at all."

**Fax.** Practice administration's verdict on referral intake is literally "differentiator, *if scoped to fax*" — the fax is the referral channel, and the practices that lose money lose it there. It further proposes a confirmed-fax-destination registry (an unverified outbound fax number is a breach) and outbound status "the way the sender sent — fax in, fax out." Platform independently proposes a **manual connector** modeled inside the integration spine so that "fax, paper, and payer-portal work" keep the trace and status surface complete rather than silently ending wherever automation ends.

**Extension — the beta overview's own system diagram already promises both channels and no atomic owns either.** The diagram reads `staff · telephone · responsive web · kiosk · future app · fax/API`. Telephone and fax are drawn as first-class inbound channels in the packet's architecture and appear in zero of the twenty-four atomic documents. That is a promise the catalog does not keep, and it is the cheapest possible thing to fix.

---

## New patterns the hypothesis list missed

### h. The second human does not exist — and every control assumes one (5 clusters)

- Revenue cycle: "in a 2-4 person practice the maker and the checker are frequently the same person"; SY-3's gap is "what happens when nobody confirms — the dominant real-world risk in a two-person practice."
- Platform: "in a practice with one manager, self-review is theater"; "nobody reviews the reviewer"; and the cluster-level seam, "both clusters assume the practice can escalate... in the target customer there frequently is not one."
- Operations: "the escalation target is the person who wrote the escalation rule. Escalating to yourself is theater with an audit trail."
- Practice administration: "a self-approval workflow is ceremony with no separation of duties behind it."
- Front office: "in a three-person office the sender *is* the approver, and a second-approver requirement will be satisfied by clicking twice."

**The replacement is identical across all five and nobody coordinated it: reversibility, visibility, and delay instead of approval.** Front office proposes 30-day merge reversal and a ten-minute campaign undo; revenue cycle proposes "prefer reversibility over approval" plus a daily digest; operations proposes retrospective reopen instead of prospective review; practice administration proposes friction-plus-receipts, an owner's weekly digest, and 24-hour delayed dangerous actions; platform proposes owner-approved elevation by phone push and observation-based least privilege. Five independent derivations of one principle is the strongest signal in the review set. It also happens to resolve the timely-filing conflict, because nothing that cannot stall cannot expire.

The one carve-out all five preserve: **segregation of duties is genuinely impossible at this size, and unacknowledged impossibility is the finding.** Platform's compensating-control acknowledgment — dated, signed, filed automatically — is the correct answer and should be product-wide, not permission-specific.

### i. Learn from your own history beats configure from authority (6 clusters, unclaimed as a principle)

This appears as an *alternative solution* twenty-plus times across all six reviews and is never named as a principle:

- Expected allowables from your own posted remits rather than a contract (revenue integrity D3 and the Learned Allowable Baseline; revenue cycle CC-1 alternative 2 — arrived at independently).
- Scrubber rules from your own rejection corpus ("the last four times you billed this pair without a modifier, this payer rejected it").
- Payer payment timing from your own remittance history rather than a status transaction.
- Patient estimates from your own historical responsibility for that payer-and-service pair.
- Booking constraints inferred from twelve months of actual bookings ("you have never booked a new patient after 3pm; enforce that?").
- The service catalog clustered from last year's charge lines; the payer list derived from prior remittances; the fee schedule seeded from Medicare times one multiplier.
- Setup pre-filled from the practice's own W-9, prior CMS-1500 or 837, one remittance, and NPPES lookups — "a prior claim is a complete, authoritative statement of the practice's own billing structure; it is the cheapest master-data import in healthcare and nobody uses it."
- The credential inventory seeded from the CAQH ProView export the practice must maintain anyway.
- Deadlines derived from facts already in the system rather than from SLA policy.
- Reference bands from the practice's own trailing twelve rather than a benchmark network.

**This is the single largest deletion available to the packet**, because setup abandonment is independently named as the most likely cause of beta failure, and configuration surfaces are independently named as the largest source of enterprise smell. Stating it as a principle — *the practice's own artifacts and history are the primary configuration source; hand authoring is the fallback* — retires a dozen screens at once.

### j. Six independent "one screens" for the same three people (6 clusters)

Every cluster converged on a single-page answer, and none of them is aware of the others: the **Front-Desk Day Cockpit**; the **Morning Huddle Page** (which operations then merges with the inbox and the manager control surface); the biller's **one page, five stacks**; the owner's **one page, three questions** plus the bookkeeper's one button; platform's **Practice Health, four tiles**; and practice administration's **negative-form readiness board**. At 2-4 staff with role compression these are not five audiences — they are the same two or three people at different hours of the same day. Nobody composed them, and composing them is the "most intuitive" answer. §3 does it.

### k. Four competing spines (3 clusters, one flagged explicitly)

Revenue cycle's per-visit financial thread, platform's end-to-end transaction trace, platform's correlation-as-first-class-data, and practice administration's readiness graph are four cross-cutting contracts that all claim to be *the* spine, plus front office's readiness contract and previsit plan, which are the same computed diff evaluated twice. Platform flags two of the collisions itself and warns that both will be half-built. **The resolution is structural, not editorial: the thread is the identity, readiness is a computation over it, and evidence is a rendering of it.** Nothing should ship until that is written down once.

### l. Day one is the highest-probability beta failure and is owned by nobody (4 clusters)

Platform: migration "is the highest-probability cause of beta failure: bad balances destroy trust in the financial system permanently and in week one," and migrated data has no ledger provenance, no rollback plan for a failed cutover, and no mapping of legacy users to roles. Revenue cycle: opening balances and the financial migration boundary are a named missing feat — "the first beta customer has this problem on day one, and the money chain must degrade gracefully rather than present a cliff." Practice administration: scope migration brutally and *recommend running off old AR in the legacy system*. Revenue integrity: the subledger has no representation for pre-system history.

Three reviewers independently recommend the same three things: **migrate open items only, run progressively rather than cutting over, and make the practice sign a one-page reconciliation of the balances.** No document owns any of it, and the OpenEMR-scoped migration kit is likely aimed at the wrong source system entirely.

### m. The clinical boundary has no waiting state, no correction loop, and no outage behavior (4 clusters)

- Operations: administrative work routinely blocks on a clinical action (note unsigned, therefore no charge). Boundary discipline correctly puts that task in the EHR, but the *consequence* — an unbilled visit aging toward a filing limit — lives here with no representation, "or these items will simply vanish from both systems."
- Platform: no EHR outage behavior specified ("at this size that is a whole-day operational question"); the clinical→administrative correction loop is undefined (a corrected date of service or diagnosis after the claim went out must drive a corrected claim); patient identity reconciliation across the boundary is a certainty at go-live and unmodeled; and *which system is authoritative for the appointment* is left implicit, which platform calls "the single most contested object in every PM/EHR pairing."
- Front office: nothing describes how a contact restriction reaches the EHR, "which will happily call the house"; duplicates created in the EHR propagate inward and only one direction of the identity contract is described.
- Revenue cycle: every provenance chain terminates at "the EHR said so," and that seam is where real disputes live.

The boundary is the product's identity claim and it is specified in one direction only.

### n. Licensed intellectual property is a product-shape input treated as a footnote (2 clusters — sub-threshold but decision-forcing)

Revenue cycle raises procedural code-set licensing as "a genuine legal question for a self-hosted open-source product [that] should be a first-order design input, not a footnote," and proposes a split-by-license-status distribution model. Platform raises X12 implementation-guide licensing with "no proposed distribution model." Both determine whether the catalog ships, ships as a bring-your-own overlay, or does not ship — which changes onboarding, the connector build, and the open-source distribution story. Two clusters is below the three-cluster threshold, but the consequence is a fork in product shape, so it belongs on the decision list.

---

# 3. The unified mental model

The reviewers produced six cluster-level models. They compose cleanly, because five of the six are describing the same object from different chairs.

> **Six nouns and one verb. Everything else is a view.**

## The concepts

**1. The Day.** The unit of time and the unit of completion. A day opens with a huddle and closes with a close; the close is what converts "in progress" into "done and provable." Weeks, months, quarters, and years are longer days with their own open and close — the month closes because thirty days closed, and month-end is therefore just printing. Every metric period, every queue horizon, every ritual, and every evidence artifact hangs off this. *Absorbs: operations' huddle and end-of-day close, revenue integrity's daily balanced day and dissolved period close, revenue cycle's "a day is not finished until every arrived patient has a billing disposition," front office's end-of-day cleanup.*

**2. The Thread.** One per visit. The durable spine everything attaches to, with exactly four properties at any instant: a **station**, an **age**, a **dollar amount**, and an **owner**. Threads that do not belong to a visit are first-class and explicitly enumerated — a credit balance, a referral not yet matched to a patient, a payer records request, a recoupment case, a pre-openPractice migrated balance. Work is a thread stuck at a station longer than it should be. Provenance is the thread's history; the appeal packet, the secondary claim, and the patient's "what happened to my bill" are renderings of it. *Absorbs: revenue cycle's financial thread, platform's transaction trace and correlation identity, front office's per-appointment task list, the AI decision record.*

**3. Readiness.** One computation, stated once: **what this needs, minus what we know is true right now.** It is never a screen and never an API the user meets. It is the sort order of the day, the patient's task list on their phone, the claim's hold reason, the booking warning, the referral's blocking question, the waitlist offer's eligibility test, and the desk's wave-through signal — all the same computed list rendered to whoever can close it fastest. It is derived by dry-running the downstream operation (build the claim in memory; if it validates, it is claim-ready), so it can never drift from what actually fails. *Absorbs: front office A6/E1/E6/F3, practice administration 1.4/2.5/7.1 — six feats and three "readiness contracts" become one function.*

**4. The Promise.** A dated assertion with an owner. *Dr. Chen is billable to UHC at Main St. for office visits starting April 12. This price holds until June 30. This authorization holds eight units until September 30. This consent is valid until revoked. We told this patient $180 on March 3.* Readiness is evaluated against promises. Effective dating disappears as a concept because the user only ever does two things: **schedules a change** ("starting on ___") and **looks at a day** ("as of ___"), with one calendar listing every promise that changes soon and who owns it. Financial records freeze their resolved promises at creation, so history is immutable by construction. *Absorbs: practice administration's effective-dated graph, enrollment cases, expiry watch, price-by-date-of-service, form versions; revenue cycle's fee versioning and authorization registry; front office's consent and estimate versioning.*

**5. The Money Line.** The externally verifiable fact: a line on the bank statement, a check, a card batch, a deposit. Every dollar inside the system terminates in one, and every money line is explained by exactly one thing or it is red. This is what makes "does it tie" answerable by the owner in their own banking app, and it is where PLB takebacks, merchant fees, chargebacks, returned items, payer interest, and cash over/short find their only possible home. *Absorbs: revenue integrity F8/F9/F12 and the bank-line inbox, revenue cycle's deposit reconciliation gap, the daily balanced day's evidence.*

**6. The Roster.** Who works here — humans and robots — in what job, at which locations, from when to when. Access, assignment, coverage, absence, offboarding, automation authority, and the outside billing service are all pure functions of it. There is no permissions screen; there is a staff list. Adding a person, changing their job, or ending their employment is the complete access-administration surface, and a robot is a roster entry with a job title that can be fired the same way a person can. *Absorbs: platform feats 1/3/4/11/12, operations A2/A4/B4.*

**And one verb: Close.** Closing a day, a week, a month, or a quarter is the act that produces evidence. Nothing in the system is ever *prepared* for an audit, an insurance renewal, or a bookkeeper; the artifacts are the exhaust of a close that had to happen anyway.

## The six primary surfaces

Everything reviewed renders into six screens. Nothing else gets top-level navigation.

| Surface | What it is | Who opens it, when |
|---|---|---|
| **Today** | One printable page: today's schedule sorted by readiness with the named blocker and its one-click fix; what is stuck, by money × age; today's five numbers; yesterday's close status. It opens the day at 8am, is the home screen all day for everyone, and closes the day at 5pm. | Everyone, constantly |
| **The Patient** | One page: who's involved, what's true, what's owed, what's been said, what's next. The **household** is the unit wherever a family shows up, signs, pays, or is reminded. | Whoever is on the phone or at the desk |
| **The Thread** | One visit, one vertical timeline, one station, one age, one dollar figure, one owner, one next action — readable as a story, top to bottom. Also the patient's own view, the appeal packet's source, and the undo surface. | The biller; the person answering "why hasn't this paid" |
| **The Money** | Did it come in (collected this week against a 13-week band, plus tomorrow's predicted deposit); what is stuck (three rows, dollars and counts); does it tie (one light per bank line, only unexplained is red). Bookkeeper's version: one button and one reconciliation certificate. | Owner, five minutes weekly; bookkeeper, monthly |
| **The Practice** | The roster; what is expiring; what changes when; the binder. The negative form of readiness — everything that will not be true in the next fourteen days, with a name on each line. | Office manager, weekly |
| **The Pile** | One inbound inbox for everything that arrives — fax, upload, scan, payer mail, inbound text, a logged call — with one clock, one name per page, and one disposition. | Whoever is at the desk, every morning |

## What becomes a mere view

- **Three readiness contracts, a previsit plan, a completeness coach, and an arrival readiness percentage** → the Readiness computation, rendered on Today and on the patient's link.
- **A financial thread, a transaction trace, a correlation identity, and an AI decision record** → the Thread.
- **Seven proposed worklists** (front office counts them; revenue cycle counts seven queues in one cluster; operations counts six source domains firing into one list) → stuck Threads, sorted by money × age, on Today.
- **Effective-dated graphs, enrollment cases, expiry watches, fee versions, form versions, consent versions, authorization registries** → Promises, surfaced as one scheduled-changes calendar and one expiry radar.
- **Deposit workbench, EFT-ERA reassociation, merchant settlement, PLB handling, period close, accounting export** → Money Lines plus the Close verb.
- **RBAC matrix, break-glass, session policy center, delegation, substitute authority, routing, team views, automation scopes, API clients** → the Roster.
- **Fax intake, unmatched intake queue, referral intake, the communications inbox, phone-call capture** → the Pile.
- **Evidence packs, disclosure accounting, review campaigns, configuration-as-evidence, metric registry** → artifacts emitted by Close; the binder is a rendering, never a workspace.
- **The app, the kiosk, the mobile previsit surface, deep links, progressive permissions, channel capability matrix** → rendering decisions for one visit task list with a URL that needs no account and a staff mirror that can close any item on the patient's behalf.

**The test.** Every proposed feature must answer: *which of the six nouns does it touch, which of the six surfaces does it appear on, and what does it delete?* A feature that introduces a seventh noun or a seventh surface needs an argument, not a specification.

---

# 4. Consolidated missing-feat catalog

Seventy-four missing feats were proposed across six reviews. Deduplicated to fifty-two below. **⚑ marks convergence** — proposed independently by two or more reviewers, which is the strongest available evidence that the gap is real.

Priority: **B** = beta-blocking (the loop is broken, unsafe, illegal, or structurally incomplete without it) · **V** = beta-valuable · **P** = post-beta.

## The day, the desk, and the channels

| Feat | One line | Pri |
|---|---|---|
| ⚑ **End-of-Day Close** | Non-configurable evening checklist: every arrival has a billing disposition, every payment posted and matched to batch and drawer, no-shows dispositioned, tomorrow cleared; emits a signed "day closed" record; an unclosed day appears on tomorrow's huddle. *(3 reviewers)* | **B** |
| ⚑ **Morning Huddle Page** | One printable page generated at 6:30am and emailed: today's schedule with per-appointment risk flags, who's in and out, overdue work, yesterday's five numbers, any unclosed day. Also the landing screen. *(2 reviewers)* | **B** |
| ⚑ **Front-Desk Day Cockpit** | The one screen a receptionist lives in: every appointment with arrival state, readiness, balance, unread reply, previsit status, sorted by who needs a person. *(2 reviewers; merges with the huddle)* | **B** |
| ⚑ **Phone Console And Call Disposition** | Caller-ID screen-pop, one-click book/message/callback from the call, a required disposition that becomes a work item or a logged outcome — plus a five-second "add a thing" capture from any screen. *(3 reviewers)* | **B** |
| ⚑ **One Inbound Pile** | A single searchable, assignable, aging inbox for every inbound page — fax, upload, scan, payer mail — with disposition buttons routing to referral, patient, payer correspondence, or trash. *(2 reviewers)* | **B** |
| **Walk-In And Same-Day Access** | An entry path with no prior appointment: find-or-create identity, decide capacity, slot into a same-day queue, run a compressed readiness path at the desk. | **B** |
| **Desk-Side Real-Time Exception** | Nobody owns "the patient is standing here and something is wrong" — no card, wrong insurance, no guardian, unmerged duplicate. Every readiness blocker needs a staff-side fifteen-second resolution. | **B** |
| **Recurring Practice Calendar** | Ship the operating rhythm pre-populated: credential and contract reviews, timely-filing sweeps, quarterly reconciliations, annual policy/BAA reviews, fee-schedule updates. Merges with the compliance calendar. | V |
| ⚑ **Snooze With A Reason** | First-class deferral ("payer said call back on the 12th") that preserves original age and returns on the date. Without it staff close valid work to clear red and every aging signal becomes a lie within a month. | **B** |
| ⚑ **Coverage Day And While-You-Were-Out** | One control to mark yourself out (pooling your work, flagging your recurring obligations) plus a return briefing. Replaces the entire delegation subsystem. *(2 reviewers)* | V |
| **Shift Handoff Log** | What is unresolved when the 2pm person leaves. | V |
| **Payer Follow-Up By Batch** | Group stalled claims by payer so one twenty-minute call resolves twelve items, with bulk disposition and one shared evidence note. Per-item queues fight the dominant work pattern. | V |
| **Provider Absence And Mass Schedule Disruption** | The doctor is out sick: reschedule twenty-two patients, notify all, offer alternatives, track who landed somewhere. Happens several times a year; touched by nothing. | V |
| **Recall And Overdue-Care Outreach** | The demand engine — due-date-driven outreach administered here from clinical recall dates owned by the EHR. Feeds the waitlist, the campaigns, and the schedule. | V |
| **Nurse-Visit And Standing-Order Booking** | Injections, immunizations, BP checks, wound care: appointment types consuming nurse rather than provider capacity. | P |

## Front office and identity

| Feat | One line | Pri |
|---|---|---|
| ⚑ **Self-Pay And Good Faith Estimate Lane** | See the money section — it is one feat with a front-office face. *(3 reviewers)* | **B** |
| **Reschedule As A First-Class Operation** | Carry forward the previsit plan, estimate, eligibility result, queued cadence, and issued link; invalidate exactly what genuinely changed. Cancel-plus-create orphans four artifacts silently. | **B** |
| **Patient Merge Aftermath And Reversal** | What follows a merge — appointments, in-flight claims, balances, signed forms, issued links, live threads, sent statements — plus a 30-day reversal restoring both records intact. The packet detects duplicates and stops. | **B** |
| **Household And Family Coordination** | Booking, checking in, preparing, signing, and paying for a family block as one act against one guarantor account. No household object exists anywhere in the packet. | V |
| **Minor-To-Adult Authority Transition** | Guardian roles expiring at majority, re-consent, communication re-routing, confidential adolescent services in the window before. Currently an unscheduled data-integrity bomb. | V |
| **Interpreter, Accessibility, And Accommodation Scheduling** | Language, ASL, wheelchair, service animal, extra time, companion — as appointment requirements that constrain booking, trigger arrangements, appear on the day sheet, and log the arrangement as evidence. | V |
| ⚑ **Assisted Mode** | One universal keystroke: "I am doing this for the patient," recording actor, authority, and reason, on any patient-facing task. Makes channel parity one mechanism rather than a per-feature obligation — and forces a design for the signature boundary staff cannot cross. *(2 reviewers)* | **B** |
| **Referral-In As A Front-Office Entry Point** | For any specialty practice, most new patients arrive as an inbound referral, not a phone call. The funnel has no entry for it. | V |
| **Urgent-Content Safety Property** | The moment two-way SMS exists, "chest pain since last night" can arrive on a Friday at 5pm. Keyword escalation, unconditional after-hours auto-reply naming the emergency path, same-day human-review SLA. Deterministic, not a classifier. | **B** |

## Money

| Feat | One line | Pri |
|---|---|---|
| ⚑ **Self-Pay And Time-Of-Service Money Path** | A priced, discountable, receipt-bearing, GL-destined revenue path for patients who never generate a claim, plus point-of-service collection at check-in. Every path in the current packet begins with a claim. *(3 reviewers)* | **B** |
| ⚑ **Good Faith Estimate Register And Dispute Clock** | Issue, version, deliver, and retain the written GFE for uninsured/self-pay patients; watch billed-versus-estimated variance; assemble a dispute-ready evidence file at the threshold. A legal obligation currently living in an editorial note. *(3 reviewers)* | **B** |
| ⚑ **Bank Line Inbox** | The bank statement as a to-zero worklist: every credit and debit explained by exactly one settlement, fee, transfer, or named non-patient item. *(2 reviewers)* | **B** |
| ⚑ **Merchant Settlement, Fee, And Chargeback Reconciliation** | Gross vs net funding, per-batch and monthly fees, reserve holds, chargebacks and their fees, returned items — each with an expense or liability destination. *(2 reviewers)* | **B** |
| ⚑ **Payer-Level Adjustment (PLB) Ledger** | Takebacks, forwarding balances, interest, capitation, and early-payment allowances resolved to the original claim or a named non-patient account. Without it the check never equals the sum of claim payments and every affected deposit fails reassociation invisibly. *(2 reviewers)* | **B** |
| ⚑ **Manual EOB And Paper-Check Intake** | A structured path to enter a paper remittance and a paper or lockbox check that produces the same posting, variance, provenance, and statement objects as an 835. *(2 reviewers)* | **B** |
| ⚑ **Recoupment And Overpayment Case File** | Demand letter → appeal-or-agree → refund disbursement or offset election → the specific future remittance line where the offset lands. The most common way a small practice loses track of real money. *(2 reviewers)* | **B** |
| ⚑ **Secondary And Tertiary Claim Derivation** | Build the next payer's claim automatically from the posted primary remit including prior-payer adjudication amounts, plus crossover detection. 15-25% of claim volume; the packet's best provenance application; named nowhere. | **B** |
| **Corrected Claims, Voids, And Replacement Control** | Replacement and void with the original payer control number, a diff of what changed, duplicate-denial protection. | **B** |
| ⚑ **Timely-Filing And Deadline Control Plane** | One clock per claim and per appeal, seeded from payer policy, with escalation before expiry, automatic classification on lapse, and a deadline-aware escape from every human gate. *(2 reviewers, via the gate conflict)* | **B** |
| ⚑ **Learned Allowable Baseline** | Expected allowed amounts as a statistic over the practice's own posted remits (median by payer × code × modifier × POS, with n and spread), as the default expectation and the challenger to any loaded contract. *(2 reviewers, independently)* | **B** |
| **Payer Drift Alarm** | Weekly comparison of each payer's recent allowed amounts to its own trailing baseline. This, not a case workbench, is how a small practice detects a payer quietly cutting rates. | V |
| ⚑ **Enrollment-Gated Claim Hold And Retro-Release** | Hold — not reject, not submit — claims for any provider/payer/location pair not yet approved; on approval with a retroactive effective date, release every eligible held claim in one action and flag the rest for self-pay, appeal, or write-off. The highest-dollar administrative feature in the packet. *(3 reviewers)* | **B** |
| **Substitute And Supervising Billing Arrangements** | Locum (Q6 with its day limit and countdown), reciprocal billing (Q5), incident-to, split/shared, supervising-physician arrangements — so readiness answers "billable *under whom*, with what modifier, until when" instead of a confidently wrong red. | **B** |
| **Non-Visit Revenue Capture** | Time- and period-based services with no appointment to derive from: chronic care management, remote monitoring, e-visits, prolonged services. | V |
| **Workers' Comp, Auto, And Liability Billing** | A distinct payer model: claim number, adjuster, date of injury, jurisdictional fee schedule, no eligibility transaction, different statement rules. One such patient breaks four subsystems at once. | V |
| **Refund Disbursement Register** | Refunds as a liability from approval through check or ACH issuance to clearance, with the 60-day federal clock, stale-dated checks, card-refund-to-original-method, and an escheatment endpoint. | **B** |
| **Credit-Balance Clocks By Cause** | Split the bucket into unidentified receipts, patient credits, and payer overpayments; each with a named clock showing days *remaining*, and a "why do we have this" classifier that determines the resolution. | **B** |
| ⚑ **Daily Balanced Day** | Cash drawer with float and over/short, card batch, posted receipts, deposit slip — five minutes, one marker. The practice's real control point, replacing the monthly close. *(2 reviewers)* | **B** |
| **Money-Day Predictor** | Tomorrow's expected deposit from settled card batches, funded ERAs, and known payer cycles. Predicting the deposit before it lands is what earns trust in every other number. | V |
| **Patient Financing Settlement** | Third-party financing as a full-balance settlement net of a merchant discount, with recourse and refund behavior. | P |
| **Denial-To-Rule Promotion** | Every worked denial promotes a scrubber rule in one action; opt-in and anonymized, a shared community rule pack. | V |
| **Cross-Deployment Payer Benchmark** | The federated version of payer accountability — opt-in, anonymized, aggregation-protected, with published definitions. A differentiator no proprietary vendor can copy. Replaces the duplicated SY-4/CL-6/F6 dashboards. | P |

## Practice administration

| Feat | One line | Pri |
|---|---|---|
| **Provider Onboarding And Departure Runway** | Backwards-planned countdown from hire date to first-billable date across NPPES, CAQH, and each payer — and its mirror: terminations, directory removal, panel reassignment, open referrals, in-flight claims, records custodianship. Departure currently fragments across five documents. | **B** |
| ⚑ **Price-By-Date-Of-Service** | Resolve self-pay prices, offering prices, and GFE amounts by service date so a mid-cycle change never reprices a prior estimate, and stale estimates surface as work before the patient is billed. | **B** |
| ⚑ **Scheduled Changes Calendar** | One page of every future-dated change — price, provider start, enrollment effective date, fee schedule, form version — with owner and one-click cancel. Makes effective dating visible as events rather than as a model. *(2 reviewers)* | **B** |
| ⚑ **Practice Change Log With Undo** | One reverse-chronological human-readable feed of every configuration change with revert. Replaces versioned configuration sets, promotion, and rollback entirely at this scale. *(2 reviewers)* | **B** |
| **Payer Records-Request Tracker** | "A payer asked us for documentation" as a channel-agnostic object with deadline, claim, what was requested, what was sent, how, and the acknowledgment. Usable today over fax and portal; the natural home for X12 275 later. | **B** |
| **Confirmed Fax Destination Registry** | Verified fax numbers, contacts, and preferred format per referring office, so outbound status and records never go to an unverified number. Doubles as the referral-source master record. | V |
| **NPPES Sync And Drift Watch** | Poll the public registry, diff against local facts, raise plain-language work when they diverge. | V |
| **Misdirected PHI Disposition** | A first-class disposition for inbound documents that are not ours, with notify-and-destroy handling that "trash" does not provide. | V |
| **Location Opening Kit** | The six weeks of work opening a site creates: NPPES address, payer location adds, POS defaults, templates, statement addresses, merchant destination, and a computed billable-on date. | P |

## Platform and trust

| Feat | One line | Pri |
|---|---|---|
| **Same-Day Offboarding Switch** | One action that kills live sessions, revokes tokens the person created, forces rotation of shared credentials, reassigns open work, removes them from alert routing, and files a dated termination-of-access record. The most frequent real HIPAA access failure at this size, performed by no feat. | **B** |
| **Right Of Access Clock** | Patient and representative record requests as a tracked workflow with identity verification, format choice, fee guardrail, a visible 30-day countdown, PHI-safe delivery, and a completion receipt. The most-enforced HIPAA provision against small practices; entirely absent. | **B** |
| ⚑ **Credential And Certificate Expiry Radar** | One inventory of every secret, key, certificate, license, and portal login with an owner, an expiry, a what-breaks-if-this-dies note, a test-before-switch rotation flow, and a countdown task. Expired credentials are the leading cause of small-practice integration outages. *(3 reviewers, counting provider credentials)* | **B** |
| **BAA-Gated Vendor Activation** | No connector, vendor, clearinghouse, processor, fax service, or backup destination activates without a linked, dated, unexpired BAA; expiry generates renewal tasks. Converts paperwork into a precondition that cannot be forgotten. | **B** |
| ⚑ **Silent Failure Detector** | Baseline the practice's own normal volumes and alert on the *absence* of expected activity — no remits for six days, claims sent down 90%, zero acknowledgments since Tuesday, a detector that has emitted nothing in a week. Catches the outages error monitoring structurally cannot see. *(3 reviewers)* | **B** |
| **Test/Production Firewall** | Structural bidirectional isolation: real patients can never reach a sandbox endpoint, synthetic patients can never reach a production claim, payment, or message. Enforced at the spine, visible as a banner, undefeatable by configuration. | **B** |
| **Day One: Migration Boundary And Progressive Activation** | Migrate open items only; a signed one-page balance reconciliation as the cutover gate; rejects become work items not a report; a defined legacy read-only period; an explicit pre-openPractice provenance boundary; and progressive activation (schedule, then eligibility, then charges, then claims) instead of a cutover event. *(3 reviewers)* | **B** |
| **Recovery Day** | A scheduled quarterly 45-minute human exercise: restore into a scratch site, verify five recognizable facts, verify connector credentials still work, prove the immutable copy cannot be deleted, record wall-clock RTO, print a signed certificate. | V |
| **Upgrade Safety Rail** | Pre-upgrade snapshot, post-upgrade smoke suite (one eligibility check, one claim build, one statement render, one payment authorization against sandbox), automatic rollback, configuration-drift report. A practice with no IT cannot be its own safety net. | **B** |
| ⚑ **Compliance As A Dated To-Do List** | One inbox holding every periodic obligation — risk analysis, access review, restore drill, BAA renewals, training, policy review, small-breach filing — where completing the task is what emits the evidence. Replaces the privacy workspace, the review campaigns, and most of the pack ceremony. *(2 reviewers)* | V |
| **Breach Response Runbook With A Clock** | Containment steps, who to call with counsel and policy pre-stored, the four-factor assessment pre-filled from the ledger, a visible 60-day countdown, letter templates, the portal checklist, and an automatic legal hold suspending retention purges. | V |
| **Annual Risk Analysis On Rails** | Generate 70-80% from live configuration and observed operation, then ask twelve questions about paper, premises, workstations, and vendors. | V |
| **Support Access With Consent** | A time-boxed, consent-gated, fully-logged path for an openPractice or MSP engineer, with a PHI-scrubbed diagnostic bundle. Without it, real support happens over screen-share using the office manager's credentials, voiding the entire access position. | **B** |
| **Waiting-On-Clinical State** | An explicit, visible, measured but unowned state for administrative work blocked on an EHR action, so an unbilled visit aging toward a filing limit does not vanish from both systems. | **B** |
| **External Participant Scoping** | The outsourced biller or contracted RCM vendor as a scoped, BAA-covered, offboardable actor. At 2-4 staff this is more likely than a second in-house biller and it is modeled nowhere. | V |

## Intelligence

| Feat | One line | Pri |
|---|---|---|
| **Practice Health One-Pager** | A monthly auto-generated PDF for the owner: cash in, charges, collections vs expected, AR aging, visits vs same month last year, the three biggest problems with dollars attached and one recommended action each. | V |
| **Anomaly Watch** | Deterministic threshold-and-trend alerts on a fixed signal set; rules, not models; every alert names the records behind it and links to the work. | V |
| **Peer Context Without A Network** | Published, sourced specialty reference bands plus the practice's own trailing twelve as the primary comparison. Answers "is 9% no-show bad?" on day one and defuses the benchmark question. | V |
| **Data Trust Panel** | A small always-reachable list of everything that could currently make the numbers wrong — unposted charges, unmatched deposits, claims in unknown states, a connector silent today. Prevents the trust collapse that kills every small-practice dashboard. | V |

---

# 5. Top 10 reframes, ranked

Ranked by leverage: how much the restatement deletes, and how much of the packet's stated differentiation it converts into something a staff member touches.

**1. From continuous flow to a day with a beginning and an end.**
A practice does not experience a queue; it experiences Tuesday. A huddle opens the day, a close finishes it, and work is what remains in between. *Deletes:* the SLA apparatus, escalation ladders, the period-close ceremony, and the unanswerable "did we do everything?" It supplies the natural home and natural owner for missing-charge detection, drawer reconciliation, metric-period correctness, and evidence generation — four problems that currently have no completion signal.

**2. From "readiness is a contract you query" to "readiness is the sort order of the day."**
Readiness should not be an API other domains consult; it should decide which appointment is at the top of the receptionist's screen and what button sits beside it. *Deletes:* three separately-specified readiness contracts, the previsit plan object, the completeness coach, the readiness API, and the divergence between what staff think is missing and what the patient is asked for. The front-desk cockpit falls out for free.

**3. From approval gates to reversibility, visibility, and delay.**
There is no second person. Humans confirm only what cannot be undone; everything else is reversible, appears in a daily digest, and — for the genuinely dangerous half-dozen actions — takes effect after a cancel window. *Deletes:* the generic approval framework, the merge adjudication queue, access-review campaigns, supervisor review of task closures, configuration-promotion approvals, and the cutover control room. It also resolves the timely-filing conflict outright, because nothing that cannot stall can expire.

**4. From provenance as an audit trail to provenance as a generator.**
Every provenance investment must name the artifact it emits and the minutes it saves — the appeal packet, the secondary claim, the rebill list, the stuck-money worklist, the preventable-denial count, the undo. If no artifact can be named, that link stores less. *Deletes:* the provenance viewer, the AI-specific decision ledger, and one of the two competing correlation systems. It converts the packet's most defensible claim from a research asset into a purchase.

**5. From modeling the contract to remembering the payer's promises.**
Expected allowables are a statistic over the practice's own posted remits, not a derivation from a contract nobody will load. The contract becomes evidence for a human argument, not an executable artifact. *Deletes:* the term-precedence hierarchy, the fee-schedule loading project, and the approval workflow on terms — while absorbing sequestration, multiple-procedure reduction, site-of-service differentials, and mid-year fee changes for free, and making variance detection possible on day ninety without any contract work.

**6. From reconciliation as a report to "every bank line has an owner."**
Reconciliation is a to-zero inbox keyed to lines on the bank statement; unexplained is red and nothing else is. *Deletes:* the batch-matching engine and the expected/observed duality, and collapses deposit matching, EFT-ERA reassociation, merchant fees, chargebacks, PLB takebacks, and returned items into one finishable task the owner can verify in their own banking app.

**7. From configuring the practice to teaching openPractice what your last claim already knows.**
Setup is confirmation of facts documented elsewhere — the W-9, a prior claim, one remittance, five NPI lookups, a CAQH export, last year's charge lines — never original authorship. *Deletes:* most of the setup wizard, the service-catalog authoring surface, the payer list, the fee schedule, the credential inventory data entry, and the booking-rule authoring problem. Setup abandonment is independently named as the most likely cause of beta failure; this is the direct cure.

**8. From a permission model to "payroll is the access control system."**
Access is a pure function of employment facts: who works here, in what job, at which locations, from when to when — and robots are on the same list with job titles and end dates. *Deletes:* the RBAC matrix editor, access-review campaigns, delegation and substitute-authority objects, routing rules, automation scope negotiation, the temp-expiry problem, and the offboarding gap, in one move.

**9. From channels to one task list per visit, rendered anywhere, closable by anyone.**
Build the visit task list once, give it a URL that needs no account, let staff render and close it on the patient's behalf. *Deletes:* the app-versus-web debate, kiosk parity as a feature, cross-device resume, deep-link matrices, the progressive permission ladder, and the channel capability matrix — 22 feats' worth of surface. It also makes the app falsifiable: if it does not beat the plain link by a stated margin, it does not ship.

**10. From editing the practice to scheduling a change — and looking at any day.**
Two affordances, "starting on ___" and "as of ___", plus a calendar of upcoming changes with cancel, plus financial records that freeze their resolved context at creation. *Deletes:* date-range grids, versioned configuration sets, promotion pipelines, multiple environments, and the policy-simulation harness — while making history immutable by construction rather than by governance.

*Runners-up, each cheap and each deleting real surface:* **the claim decides what is required and the patient's history decides what is asked** (collapses adaptive requiredness, the completeness coach, the AI interpreter, and duplicate entry into one property on every field); **drive every appointment to an explicit yes or no by T-24h** (no model, no bias audit, no cold start — and the residue is a call list); **an empty slot is a sales lead with a twenty-minute shelf life** (highest revenue per unit of effort in the packet).

---

# 6. Contradictions and sequencing risks

## 6.1 The headline revenue-integrity differentiators appear in no release slice — **the most serious finding in this section**

Traced by the revenue-integrity reviewer and independently confirmed here: participation registry, expected-allowable calculation, remittance variance detection, underpayment work, credit-balance and refund control, patient estimates and GFE, and contract performance map to **no slice**. Slice 0 configures "core policies" and a clearinghouse route but never establishes a payer product dictionary or an allowed-amount schedule, while Slice 2 assumes "versioned codes and fees." Slice 1 is titled "patient access and financial preparation" and prepares no finances — it runs eligibility and authorization and stops. Slice 2 computes patient responsibility only *after* adjudication.

**Consequence:** the beta as sequenced posts remittances and exports a journal without ever forming an expectation — which is precisely the OpenEMR behavior the packet exists to beat. The differentiator becomes post-beta silently, without a decision ever being taken.

**Adjudication.** Do not defer; substitute. Adopt the learned baseline (reframe #5) into Slice 2, where it costs a median query over data the beta already generates and needs no contract project. Promote estimates, GFE, and time-of-service collection into Slice 1-2 with a named money path. Move the contract-loading engine, the underpayment workbench, and contract performance to post-beta explicitly. When Slice 2 runs long — and it will — **cut the contract-expectation engine, not the reconciliation**: reconciliation to a bank line cannot be simulated later and its absence makes every other number unverifiable.

## 6.2 Timely filing versus the human-approval gate — **an unacknowledged direct conflict**

The constitution mandates human authority over consequential financial action. Claim submission is consequential. A gate that stalls a claim past its filing deadline destroys the money the gate exists to protect. The revenue-cycle review names this and names the compounding factor: the timely-filing clock is an *adopted gap with no owner, no surface, and no stated interaction with the gates that can stall it*. Pattern (h) supplies the aggravator — in a two-person practice the dominant failure of any gate is that nobody confirms, and the reviewers call the result **queue rot**.

**Adjudication.** Three rules. (1) Gate only two axes: money leaving the practice, and commitments leaving the building. (2) Every gate carries a deadline-aware escape — when the clock reaches a threshold, the action proceeds with notification and undo rather than waiting. (3) Prefer reversibility over approval everywhere else. Express the whole governance posture as a shipped four-tier policy table (auto and reversible; proceeds with notification and undo; requires confirmation; requires two people) with a default assigned to every financial action, not as a configurable framework. This satisfies the constitution and eliminates the conflict.

## 6.3 The estimates and Good Faith Estimate hole — **a legal obligation with no owner**

Three reviewers independently found it. The normalization document records the GFE correctly as a bullet and nothing carries it forward. There is no estimate, no GFE, no time-of-service collection, and no self-pay path in any of the four slices. Three consequences, each bad: the beta collects no money before or at the visit (the highest-yield cash lever a small practice has); a product that touches self-pay billing without issuing or retaining a GFE exports legal risk to the customer; and **cash and self-pay patients do not exist anywhere in the revenue-integrity cluster** — every path begins with a claim, so a practice with 8% self-pay has 8% of its revenue outside the entire integrity model, including its discount policy, its receipts, and its GL destination.

**Adjudication.** Promote to beta-blocking. Split into two products that must not be blurred: an *insured pre-service estimate* (a range with an honest error bar, used in a front-desk conversation) and a *self-pay Good Faith Estimate* (a document with required elements, delivery timing, retained copy, and a dispute clock). Seed the insured estimate from the practice's own historical patient responsibility, which needs no contract data and degrades gracefully.

## 6.4 Reviewer frame divergence: 2-4 staff versus 6-15

The platform reviewer graded against a 6-15 person practice with a nine-person example; every other reviewer used 2-4. This systematically biases platform's verdicts *toward* governance machinery — and even so, that reviewer called the matrix editor questionable, the console questionable, disclosure accounting a defer, and the public API program a defer.

**Adjudication.** Hold 2-4, per the archetype and beta overview. Platform's verdicts are therefore conservative and its conclusions hold a fortiori: at 2-4 staff, the matrix editor, the console, disclosure accounting, and the developer program are not merely questionable, they are defers. Add one correction the frame difference obscured: at 2-4 staff the **outsourced biller is more likely than a second in-house biller**, which makes external-participant scoping a real requirement and makes internal team-sharing a fiction. The archetype document should say so explicitly.

## 6.5 Four spines, one identity

Revenue cycle's financial thread, platform's transaction trace, platform's correlation-as-first-class-data, and practice administration's readiness graph all claim to be the cross-cutting contract; front office's readiness contract and previsit plan compute the same diff twice. Platform flags two of the collisions itself and warns both will be half-built.

**Adjudication.** One thread is the identity (minted per visit, with explicitly enumerated non-visit threads); readiness is a computation over promises, not an identity; evidence is a rendering of the thread. Write the correlation identity as a versioned contract *before* any consumer is built: what it identifies, when a new one is minted, how children inherit, how re-association works when a partner strips it, what the "unknown" state looks like, and what happens on merge, split, and the migration boundary.

## 6.6 Contract-derived versus learned expectation

The packet's default is contract-derived with history "relegated to a signal." Two reviewers independently challenged it. The legal caution behind the packet's stance is correct; the product default is not.

**Adjudication.** Invert with the caution preserved: **the learned baseline is the operational expectation; the contract is the arbiter.** Default the beta to learned-only, make contract loading optional and per-payer, and treat disagreement between the two as the single most valuable alert in the product — it means either the contract was loaded wrong or the payer is not honoring it, and a small practice can currently detect neither.

## 6.7 Period close versus the practice's actual close

The packet models close as a first-class controlled ceremony with freezes and compensating entries. The design partner's close happens in QuickBooks, monthly or quarterly, performed by a bookkeeper. An openPractice lock will meet the first late ERA or 60-day-old chargeback and will either block the practice or be routinely reopened, at which point the control is theatre.

**Adjudication.** Replace with immutability at post time, posting-date-stamped rows, as-of queries, and versioned export batches as the only freeze. Never lock a patient's balance. If a ritual is wanted, make it the daily balanced day, which a small practice can actually perform and which catches errors thirty days sooner.

## 6.8 Certified test traffic versus what the beta must prove

Scope-and-sequencing leaves open whether certified test traffic may precede live payer exchange. The revenue-integrity reviewer challenges the escape directly: a beta that never receives a real ERA and a real merchant settlement cannot test reassociation, PLB handling, fees, or chargebacks — precisely the mechanisms that only misbehave with real data, and precisely the ones this packet exists to get right.

**Adjudication.** Require at least one live payer ERA and one live merchant settlement, at any volume, before beta exit. Test traffic is acceptable for everything upstream of the money.

## 6.9 Readiness as a gate versus the clinical reality that the visit happens anyway

Front office and practice administration both flag it and reach the same answer from opposite directions: never hard-block a booking or a visit; a readiness failure produces a signed "we knew and proceeded because ___" record that flows to a claim-hold bucket.

**Adjudication.** Hold the claim, never the patient. Three severities only: blocks booking (essentially never), blocks billing, informational. And a booked-anyway record is not an error state — it is the evidence artifact.

## 6.10 Deferring the app must not defer the patient's money view

Agreement is unanimous on deferring the companion app (independently reinforced by the unresolved trademark question on the HealthLinc name). But the carve-out matters: a mobile-web page showing "what you owe, why, and pay now" from a texted link is load-bearing for collections and for the estimate-to-payment thesis, and it currently sits inside "responsive previsit tasks" in Slice 1 with no money content.

**Adjudication.** Name the patient money view as its own beta-scoped surface, riding the same universal per-appointment link as everything else.

## 6.11 Append-only integrity versus required deletion

Platform names it as a direct, unacknowledged conflict: state retention limits, minors' rules, patient-requested restrictions, and data minimization all require deletion from a structure defined as immutable, and no feat proposes hash-preserving tombstones or chain-safe redaction. It will surface as a hard blocker at the first deletion request rather than as a design choice.

**Adjudication.** Decide it now, in the ledger's design, before the chain has a year of history to invalidate.

## 6.12 Two apparent conflicts that are not conflicts

- **Routing (defer) versus role recipes (essential).** Operations defers assignment; platform requires permission. These are different objects. Assignment is overhead at three people; permission is not. Separate them: work is unassigned by default with a soft claim, while *who may issue a refund, adjust a balance, or export in bulk* is a roster-derived fact.
- **OCR is weak (front office) versus OCR-assisted intake (practice administration).** The distinction the reviews imply and never state: OCR is fine where a downstream authority corrects it or where the source document is itself retained as the evidence (a fax, a credential PDF); OCR is not fine where the patient becomes an unpaid proofreader of a guess with no authority to check it (the insurance card, where a 271 is available and authoritative).

---

# 7. Prioritized improvement plan

## (a) Brainstorm documents needing revision

### Must revise before anything else is written (5)

| Document | The change |
|---|---|
| `beta-scope-and-sequencing` | Rebuild the slices. Add the day rituals and the roster to Slice 0; add phone, fax, and the inbound pile to Slice 1; promote estimates, GFE, self-pay, and time-of-service collection into Slice 1-2; absorb the learned baseline and variance into Slice 2; add the enrollment→claim-hold valve, secondary/corrected claims, timely-filing clocks, paper remits, PLB, merchant fees, and the bank-line inbox to Slice 2; replace cutover with progressive activation and "six firsts"; retire the test-traffic escape. Every slice must state which of the six surfaces it lights up. |
| `beta-feature-normalization` | Add three rules to the eleven-point feature contract: **the naming rule** (no screen ships with a name the office manager would not say out loud); **the valve rule** (every feat producing a verdict, score, readiness state, or metric must name the consumer that acts on it, or it is a report); **the artifact rule** (every provenance investment names the artifact it emits and the minutes it saves). Add "learn before you configure" as a design principle. |
| `feature-list-overview` | Restate the three differentiator claims as artifacts rather than architectures: appeal packets, auto-derived secondary claims, rebill lists, the stuck-money list, the quarterly binder, and undo. Retire "queryable provenance API" and "communication decision record" as headline phrasings. Add the six-surface model so the document map stops implying the navigation map. |
| `beta-overview` | Add the day/week/month/quarter rhythm and the six-noun mental model. Either give telephone and fax owning atomics or remove them from the system diagram — the diagram currently promises two channels the catalog does not own. Settle the staff-count frame and name the outsourced biller as a first-class actor. |
| `beta-launch-archetype` | Fix the frame at 2-4 staff, add the outsourced biller, add print-first as an archetype constraint, and add the day-one reality (the practice is already running, with open AR, in-flight claims, future appointments, and patients mid-payment-plan). |

### Should revise (9)

- `synthesis-revenue-cycle` — SY-1 becomes the financial thread with station/age/dollars/owner; SY-3 becomes a four-tier policy table with deadline escapes; delete SY-4 as a duplicate and replace it with the cross-deployment benchmark.
- `synthesis-operations-and-intelligence` — one list, twelve work types each with a **clearing condition**, consequence-derived deadlines instead of SLAs, the day boundary, and detector-health canaries.
- `synthesis-platform-and-trust` — re-cut entirely around the four visible surfaces and the twelve missing feats; explicitly defer the matrix editor, the console, disclosure accounting, and the public API program; decide whether practice staff ever see Frappe Desk (recommended answer: no).
- `synthesis-front-office-and-access` — collapse seven product lines into one funnel plus the day cockpit; demote the app, AI, and sensing compositions; promote the walk-in, the phone, and the desk-side exception.
- `beta-synthesis-practice-administration` — unify the three readiness contracts into one evaluator with one output shape, and give it the claim-hold valve; add supervising and locum arrangements before the evaluator ships or it will be confidently wrong.
- `beta-synthesis-revenue-integrity` — learned baseline as default, close dissolved into always-closed plus as-of, and the PLB / merchant-fee / self-pay paths added.
- `beta-payer-contract-and-reimbursement-integrity` — invert to learned-primary with contract-as-arbiter; ship MPFS as data; add sequestration, MPPR, bilateral, and site-of-service as named known deductions.
- `beta-financial-control-and-accounting-bridge` — bank line as the unit of work; the reason-code taxonomy defined first because it is the GL join key; QuickBooks-shaped cash-basis default; the reconciliation certificate.
- `beta-implementation-and-configuration-lifecycle` — delete promotion, environments, simulation, and the control room; replace with paperwork-first bootstrap, change log plus undo, a sandbox clone, progressive activation, and six "firsts."

### Light-touch revisions (the remaining atomics)

Each needs its cluster review's "make it more useful" plus its named gaps folded in, with three cross-cutting edits applied uniformly: strip architecture nouns from any user-facing name; name the consumer for every verdict the document produces; and state the day/week/month rhythm the document participates in. The heaviest of these: `scheduling-and-access` (provider time off, double-booking, holidays, walk-ins, nurse visits, interpreter constraints, and "why can't I book this?"); `claims-and-remittance` (corrected claims, filing clocks, paper remits, PLB, the payer claim control number, silence-as-exception); `operations-tasks-and-workforce` (delete routing/SLA/delegation/team-sharing; add snooze, phone capture, coverage day, batch follow-up); `platform-security-and-compliance` (role recipes plus roster; offboarding, right of access, BAA gating, risk analysis, breach runbook, recovery day); `interoperability-and-exchange` (status line plus work queue plus silence detector; credential radar; test/prod firewall; enrollment tracking; the manual connector). Three documents shrink rather than grow: `healthlinc-app-onboarding` becomes a post-beta channel note with its identity and household principles extracted; `ai-intake-assistance` keeps only its deterministic twins, which move to their owning documents; `arrival-presence-detection` reduces to arrival provenance, the inclusive ladder, and the one-click delay broadcast.

## (b) New atomic documents to write

**Tier 1 — write before the next staging round (8).** These close the beta-blocking holes and each one currently has no owner.

1. **The Day: Huddle, Close, And Practice Rhythm** — day/week/month/quarter/year rituals, the close record, unclosed-day escalation, snooze-with-reason, print-first design.
2. **Self-Pay, Time-Of-Service Collection, And Good Faith Estimates** — pricing, discounts, receipts, GFE register, dispute clock, GL destination, and the estimate-versus-GFE split.
3. **Money Lines: Deposits, Settlements, Fees, And The Bank Inbox** — PLB, merchant fees and chargebacks, paper checks and EOBs, virtual cards, the daily balanced day, the money-day predictor, the refund disbursement register.
4. **Enrollment-Gated Billing: Holds, Retro-Release, And Substitute Arrangements** — the packet's highest-dollar missing feature plus Q5/Q6/incident-to/split-shared, participation as a claim-time precondition, and the onboarding/departure runway.
5. **Secondary Claims, Corrected Claims, And The Deadline Control Plane** — COB derivation from the posted primary, crossover detection, voids and replacements with the payer control number, one clock per claim and per appeal, and the deadline-aware escape from every gate.
6. **Phone, Fax, And The Inbound Pile** — call capture and disposition, screen-pop, the fax-to-referral path, unmatched intake, misdirected PHI, the confirmed fax destination registry, and the manual connector inside the spine.
7. **Day One: Migration Boundary, Opening Balances, And Progressive Activation** — open-items-only, the signed reconciliation, rejects as work, the legacy read-only period, the pre-openPractice provenance boundary, rollback, and the six firsts.
8. **The Roster: People, Robots, Access, Coverage, And Offboarding** — role recipes, same-day offboarding, absence and return briefing, temp expiry, external participants, automations as employees, and support access with consent.

**Tier 2 — write in the same round if capacity allows (7).**

9. **The Visit Thread** — the correlation identity as a versioned contract; resolves the four-spine collision and must precede any consumer.
10. **Readiness: One Evaluator, Many Consumers** — the single computed diff plus its twelve valves; resolves six overlapping feats.
11. **Recoupments, Overpayments, And Credit-Balance Clocks** — demand letters, offsets, cause classification, days-remaining, escheatment.
12. **Learned Payer Baseline And Drift Alarm** — expectation as a statistic, variance as a pattern, contract as arbiter.
13. **Compliance As A Dated To-Do List** — the compliance inbox, recurring calendar, right-of-access clock, BAA-gated activation, risk analysis on rails, breach runbook, the binder.
14. **Operational Resilience** — recovery day, upgrade safety rail, test/production firewall, silent-failure detection, credential and certificate radar, the degraded-mode card, escalation to support.
15. **Reschedule, Merge, And Mass Disruption** — reschedule as carry-forward with targeted invalidation, merge aftermath with 30-day reversal, provider-out mass reschedule.

**Tier 3 — after the next review pass (6).** Household and family coordination (with the minor-to-adult transition); assisted mode and the channel-parity invariant (including the signature boundary staff cannot cross); recall and demand generation (recall, waitlist auto-population, campaign metering); non-visit and alternate-payer revenue (CCM/RPM/e-visits, workers' comp/auto/liability, patient financing); scheduled changes and undo (calendar, change log, freeze-at-use, price-by-date-of-service, blast-radius preview); interpreter, accessibility, and accommodation.

## (c) Framing decisions to revisit

Eleven decisions, each of which changes what other documents say. They should be settled before drafting, not during.

1. **Staff-count frame** — fix at 2-4 with the outsourced biller as a first-class external actor.
2. **Expectation source** — learned baseline primary, contract as arbiter (invert the current default).
3. **Period close** — replace the ceremony with immutability at post time plus as-of queries plus the daily balanced day.
4. **Estimates and GFE** — promote from an editorial note to a beta-scoped money path.
5. **Governance primitive** — reversibility, visibility, and delay replace approval; four shipped tiers, every gate with a deadline escape.
6. **Differentiator statement** — provenance restated as generated artifacts, not as a queryable API.
7. **Spine ownership** — one thread identity, written as a versioned contract before any consumer.
8. **First external milestone** — require one live ERA and one live merchant settlement; retire the test-traffic escape.
9. **Cutover** — replaced by progressive activation; migration scoped to open items; AR runs off in the legacy system by default.
10. **Channels** — either own phone and fax with atomic documents or remove them from the system diagram; and the app's deferral must not defer the patient money view.
11. **Licensed IP** — decide the distribution model for procedural code sets and X12 implementation guides now; it forks the onboarding story, the connector build, and the open-source positioning.

## (d) Suggested order

**Wave 0 — decide (judgment, not writing).** Settle the eleven framing decisions in §7(c). Nothing below is worth writing against the current answers to items 1, 2, 3, 5, 6, and 7.

**Wave 1 — prototype three pieces of paper.** Before any document: sketch the morning huddle page, the end-of-day close sheet, and the owner's weekly money page as printed one-pagers. Four reviewers independently designed for paper, and these three artifacts are the concrete test of whether the six-noun model actually composes. If they cannot be drawn on three sheets, the model is wrong and the rest of the plan is premature.

**Wave 2 — rewrite the two governing documents.** `beta-scope-and-sequencing` and `beta-feature-normalization`. The slices and the feature contract determine what every other revision can say; revising atomics first would waste the work.

**Wave 3 — write the eight Tier-1 atomics.** These are the beta-blocking holes. They can be written in parallel; the only ordering constraint is that **The Visit Thread** (Tier 2, item 9) should be pulled forward and written alongside them, because Money Lines, Secondary Claims, and Enrollment-Gated Billing all bind to it.

**Wave 4 — revise the five must-revise and nine should-revise documents** to point at the new atomics, drop the deferred machinery, and apply the three uniform edits (naming, valves, rhythm).

**Wave 5 — write Tier 2, then re-review narrowly.** Do not re-run a full six-cluster review. Run two targeted passes only: **the money path** (referral or phone → booked → ready → charged → claimed → remitted → banked → closed) and **the day path** (huddle → work → close → evidence). Those two vertical slices touch every noun and every surface; if they hold, the packet holds.

**Wave 6 — stage to OpenSpec.** The first proposal should be one vertical slice, not a domain: *fax or phone in, booked and billable appointment out, charge, claim, remit, bank line, closed day.* Two acceptance demos carry the whole product story — **"fax landing to booked billable appointment in two minutes with zero retyping"** and **"Friday's money ties to the bank, and the office manager can see why in one screen."**

---

## Relationships

Synthesizes the six cluster reviews in this directory: [Front Office And Access](openpractice-feat-review-front-office-and-access.md), [Revenue Cycle](openpractice-feat-review-revenue-cycle.md), [Operations And Intelligence](openpractice-feat-review-operations-and-intelligence.md), [Platform And Trust](openpractice-feat-review-platform-and-trust.md), [Practice Administration](openpractice-feat-review-practice-administration.md), [Revenue Integrity And Product Shape](openpractice-feat-review-revenue-integrity-and-product-shape.md). Read against [Feature List Overview](../brainstorm/openpractice-feature-list-overview.md), [Small-Practice Beta Overview](../brainstorm/openpractice-small-practice-beta-overview.md), and [Small-Practice Beta Scope And Sequencing](../brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md). No brainstorm document is modified by this synthesis; verdicts and adjudications are non-normative and feed the next ideation and staging round.
