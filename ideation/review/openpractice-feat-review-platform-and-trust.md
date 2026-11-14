# Feat Review: Platform And Trust

Status: brainstorm
Kind: review
Summary: the platform-and-trust cluster has the right substrate (integrity-chained ledger, correlation identity, generated evidence) wrapped in the wrong surfaces (matrix editors, consoles, campaigns) for a nine-person practice whose office manager is also its compliance officer, and it omits the four things that actually generate OCR findings and downtime at that size — same-day offboarding, right-of-access requests, BAA coverage, and credential expiry.

Topics: openpractice-feat-review, platform-trust
Captured: 2026-08-26

---

## Reading frame

Everything below is graded against one customer: a 6–15 person ambulatory practice with no IT department, no CISO, no integration engineer, and one office manager who is simultaneously the Privacy Officer, the Security Officer, the HR department, and the person who works the denial queue on Thursdays. Two consequences drive most verdicts:

1. **The compliance events that actually happen at this size** are, in rough frequency order: a staff member leaves and keeps access; a patient requests their records and the clock is missed; a vendor is used without a BAA; a laptop or the server is ransomed; the annual security risk analysis was never done; the cyber-insurance renewal questionnaire arrives and nobody can answer it. Notably absent from that list: anyone designing a permission matrix, anyone reading an accounting of disclosures, anyone operating an interface console.
2. **Any screen the office manager does not open weekly does not exist.** A trust plane whose evidence is technically generated but never opened fails exactly as hard as one that generates nothing — and fails more expensively, because it consumed beta engineering.

The cluster's underlying architecture is largely correct. The surfaces are largely wrong. That gap is the review's central finding.

---

# Part 1 — Platform Security And Compliance

## 1. Minimum-necessary authority designer

**Verdict: questionable.** The *grant model* underneath (staff × service × location × object × field × action) is essential-core and cannot be retrofitted; the *matrix editor* is an enterprise artifact aimed at a role that does not exist here. A nine-person practice has roughly six distinct jobs, and the office manager will never open a six-dimensional matrix — she will hire a front-desk person and need that person working in four minutes. Risky-combination highlighting is real value, but it is value that belongs on the hiring action, not in a designer.

**Make it more useful**
- Ship **role recipes**, not a blank matrix: Front Desk, Biller, Provider, Practice Manager, Referral Coordinator, Read-Only Auditor, Outside Billing Service. Each is a named, versioned, upgrade-maintained grant set with a plain-English "can see / cannot see / can approve up to $X" summary card. The recipe is the product; the matrix is the debug view behind a "show advanced" link most sites never click.
- Make the risky combination a **hard stop at the moment of grant**, not a highlight: posting payments + adjusting balances + issuing refunds in one person is the classic small-practice embezzlement triangle. When the practice genuinely has only one biller, force an explicit **compensating-control acknowledgment** ("I accept this; the practice owner reviews the adjustment report monthly") that is dated, signed, and lands in the evidence pack automatically. Segregation-of-duties impossibility is normal at this size; unacknowledged impossibility is the finding.
- Derive scope from employment facts already in the system. Location access should follow *where the person is scheduled to work*, and provider-panel access should follow *who they are assigned to support*, so the matrix has fewer independent axes to maintain.
- Ship **field-level protection as a default, not a policy**: SSN, bank account, and full card data are masked for every role out of the box, with unmasking as a logged, reason-required, per-record action. Nobody should configure this.

**Alternate/better solutions**
1. **(Simpler) Payroll is the access control system.** The only object the office manager maintains is a staff roster: name, job, locations, start date, end date, supervisor. Permissions are a pure function of that roster. There is no permissions screen at all — there is a Staff screen. Adding a person, changing their job, or ending their employment is the complete access-administration surface, and the roster is a document the practice already keeps for other reasons.
2. **Least privilege by observation.** Ship broad-ish recipes on day one, then at day 60 show one card: "Sarah has been able to issue refunds for 60 days and never has. Remove?" One tap. Access converges to minimum-necessary through use rather than through up-front design, which is the only way it will ever converge in a practice with no security architect — and each accepted or declined suggestion is itself dated evidence of an access review.
3. **Two-key grants for the dangerous half-dozen.** Rather than modeling every permission, model the six that matter (refund, adjustment above threshold, bulk export, patient merge, unmask, permission change) as actions requiring a second named human, and let everything else ride a coarse role. Small practices need collusion resistance far more than they need granularity.

**Reframe** — Not "design the practice's authority model." Instead: **"the practice maintains a staff list, and the software makes that list mean something."**

**Gaps it leaves**
- **Same-day offboarding is entirely unaddressed.** Nothing here revokes live sessions, kills API tokens the person created, reassigns their open queue items, rotates shared credentials they knew, or produces a dated termination-of-access record. This is the most common real HIPAA access finding at this practice size and the feat is silent on it.
- No model for the **outside billing service** — a contractor org, not a staff member, needing scoped cross-practice access under a BAA, with its own offboarding when the contract ends.
- No handling of the **temp/locum/per-diem** pattern: access that should expire on a date without anyone remembering to end it.
- Break-glass (feat 3) and this feat share a substrate but no shared review surface, so a permission granted by emergency elevation may never appear in an access review.

---

## 2. Tamper-evident PHI ledger

**Verdict: essential-core.** This is the one item in the cluster that genuinely cannot be added later — retrofitting an integrity chain over a year of existing events produces a chain that proves nothing about the year that mattered. Every other feat here (evidence packs, break-glass review, disclosure accounting, incident investigation, correlation trace) is a *view* over this ledger, so it must be built first and built broadly. Basic audit logging is table stakes; the integrity chaining and the uniform capture across UI, API, background job, and automation are the differentiating part.

**Make it more useful**
- Capture **purpose-of-use at the boundary, inferred, not prompted**. Never ask staff "why are you opening this chart" — infer purpose from context (the patient has an appointment today with this provider; the user is working claim #X; the user searched by name with no work context). The rare *uninferrable* access is the interesting one, and only that one should prompt. This turns the ledger from a compliance tax into a signal.
- Log **queries and exports, not just record opens.** The real small-practice breach is a departing biller exporting the patient list to CSV, not someone opening one chart. Bulk-read detection (>N records in M minutes, any export, any report with identifiers) deserves a distinct event class and a same-day alert.
- Make the chain **externally anchorable but not externally dependent**: bench-native chained records as the floor, with a daily signed manifest optionally emailed to the practice's own accountant or attorney, or pushed to any S3-compatible object-lock bucket. The value of an anchor is that it lives somewhere the person who might tamper cannot reach — for this customer, "email the daily hash to your CPA" is a genuinely strong and genuinely free control.
- Publish a **standalone verifier** — a single script, runnable by anyone, that takes an export and says CHAIN INTACT / BROKEN AT <date>. Evidence nobody can independently check is not evidence.

**Alternate/better solutions**
1. **(Simpler) One daily hash, one external mailbox.** Skip per-event chaining ceremony in the beta; compute a daily Merkle root over the day's events, print it in the daily close email, and send that email somewhere outside the server's control. This gets ~90% of the tamper-evidence value for ~10% of the machinery, and it produces a human-readable artifact (a dated email chain) that an investigator, insurer, or attorney can actually use.
2. **Ledger as the product's undo/provenance system, not a compliance artifact.** Build it because staff constantly need "who changed this patient's insurance and when," "who wrote off this balance," "what did this claim look like before it was corrected." If the ledger's daily job is answering operational questions, it will be correct, complete, and trusted — and it will incidentally satisfy §164.312(b). Compliance-only logs rot; operational logs get fixed.
3. **Tiered retention with a sealed summary.** Full-detail events for 90 days, then compact to signed per-actor/per-patient/per-day rollups for the six-year horizon. Solves both PHI sprawl and storage growth on a small practice's server without breaking the chain.

**Reframe** — Not "prove nobody tampered with the audit log." Instead: **"any question that starts with 'who' or 'when' has a one-click answer, and the answer is provably the original one."**

**Gaps it leaves**
- **The retention/purge vs. append-only collision is unaddressed.** State record-retention limits, minor-record rules, patient-requested restrictions, and eventual data minimization all require deletion; an integrity chain forbids it. There is no design here for redacting a link while preserving the chain (hash-preserving tombstones), and this will become a hard blocker the moment the first patient asks for something to be removed.
- **No story for proving a negative.** Breach-risk assessment needs "we can show this record was never accessed outside treatment/payment/operations," which requires knowing the ledger is *complete*, not just untampered. Completeness assurance (gap detection, sequence continuity per source) is a separate mechanism and is missing.
- Events that **predate the ledger** — everything migrated from the legacy system — have no provenance, and no feat says how the ledger represents "this balance came from your old system on this date and we cannot attest to its history."
- No **ledger restore verification**: after a restore drill, is the chain still valid across the seam? A restore that silently breaks the chain destroys the entire evidence position.

---

## 3. Reviewed emergency access

**Verdict: valuable-enhancer.** Break-glass matters much less at nine people than at nine hundred, because roles are already broad — and it carries a specific small-practice failure mode where the exception becomes the daily path because the normal grants are slightly too tight. Time-boxing, alerting, and mandatory review are all correct; the danger is that "emergency access" framed as superuser elevation invites exactly the behavior it is meant to constrain. Worth building, worth building small.

**Make it more useful**
- Reframe the mechanism from *superuser elevation* to **temporary role loan**: "act as Biller until 5pm today." Scoped, comprehensible, far less dangerous, and it produces a review question the office manager can actually answer ("did Sarah need to be a biller yesterday?").
- **Alert to a second human by default, not to a log.** In a practice with one manager, self-review is theater. The alert should go to the practice owner/physician-partner by text, and the review should be a two-tap "expected / not expected" on a phone. If the answer is "not expected," that alone is the incident trigger.
- Make the **usage pattern the real output**: if break-glass fires more than three times in a month for the same person and the same object class, that is a permissions bug, not an emergency. Surface it as "Sarah has needed emergency access to refunds 5 times — add it to her role?" This closes the loop into feat 1 and makes emergencies self-eliminating.
- **Everything done under elevation is visually marked forever** in the record's history — an amber band on those entries, not just a flag in a log table.

**Alternate/better solutions**
1. **(Simpler) No break-glass; a coverage switch instead.** The actual small-practice scenario is not an emergency, it is absence: the biller is out sick and someone has to post today's remits. Model *coverage assignments* ("Sarah covers Billing while Mike is out, Mon–Wed") as a normal, scheduled, planned-ahead HR action with an end date. This handles 90% of real elevation, needs no emergency framing, and is reviewed by definition because someone had to plan it.
2. **Owner-approved elevation via phone push.** Instead of self-declared reason + post-hoc review, require a live approval: the requester taps, the owner's phone buzzes, they approve or decline in five seconds. Prospective control beats retrospective review at this scale, and the approval record is stronger evidence than a self-written reason string.
3. **Read-only break-glass.** Emergency access grants *visibility* only; nothing consequential (money movement, permission change, export, delete) is ever available through it. Most true emergencies are informational, and this removes the worst outcomes entirely.

**Reframe** — Not "grant safe emergency superuser access." Instead: **"when someone is out, their work gets covered, and everyone can see who covered it."**

**Gaps it leaves**
- No handling of **the shared-account problem** that break-glass is usually a symptom of — the front desk that all logs in as "reception." Detecting and eliminating shared credentials is more valuable than governing break-glass, and appears nowhere.
- **Nobody reviews the reviewer.** In a single-manager practice, the manager's own elevation has no natural second party unless the design explicitly routes to an owner/clinician; the feat assumes an organizational depth that does not exist.
- **After-hours reality:** the office manager at 9pm on Saturday will find *some* way in. If the sanctioned path is slow, the unsanctioned path is the server console or a shared admin password, and the ledger loses the event entirely. Break-glass must be faster than the workaround or it is counterproductive.
- No interaction defined with **offboarding**: a departed employee's break-glass grant, if any window remains open, is a live hole.

---

## 4. Identity and session policy center

**Verdict: essential-core** (the enforcement) **/ questionable** (the "center"). MFA and idle timeout are no longer optional — cyber-insurance renewal now routinely requires attested MFA on all remote and administrative access, so this feat's real business function is insurability, not HIPAA. But a small practice has exactly one policy, and the notion of a configuration center implies a decision the customer is not equipped to make and should not be offered.

**Make it more useful**
- Ship **three presets and no matrix**: Standard, Strict (multi-location or remote billers), and Shared-Workstation (front desk, kiosk). Preset choice is one question in setup, revisitable, and its selection plus date lands in the evidence pack as the attested policy.
- Solve the **shared front-desk workstation** explicitly — it is the single most common small-practice reality that generic session policy gets wrong. Long device session + fast per-user PIN/badge unlock + hard screen lock on inactivity + full per-user attribution in the ledger. Without this, the practice will disable idle timeout entirely and the whole control collapses.
- **MFA that survives a small practice:** passkeys or authenticator app first, and — critically — a documented, safe *recovery* path when the biller's phone dies on a Tuesday, because "the office manager keeps a printed break-glass code in the safe" is the design, and it must be an intentional one rather than a support call.
- Treat **connector and machine identities under the same policy object** (see B5): the practice should see one "who and what can get in" list containing both people and systems.

**Alternate/better solutions**
1. **(Simpler) Secure defaults, no policy surface at all.** MFA on, 15-minute idle lock, reauth for money and permission changes, no concurrent sessions across devices for the same human. Ship it; expose only an escape hatch that requires a written reason and is reported in the evidence pack as a deviation. Zero configuration, maximum defensibility.
2. **Device trust instead of session paranoia.** Register the four office workstations as trusted devices once; on those, sessions are generous and friction is near zero. Anywhere else, full MFA and short sessions. This maps to how the practice actually thinks ("the computers in our office" vs. "Mike working from home") and gets better security with less daily annoyance.
3. **Insurance-questionnaire-shaped output.** Generate the answers to the standard cyber-insurance identity questions (MFA coverage %, session timeout, privileged account count, last access review date) as a one-page printable. The policy center's most valuable artifact is not the policy, it is the annual answer sheet.

**Reframe** — Not "configure identity and session policy consistently across surfaces." Instead: **"the practice can truthfully answer 'yes' to every identity question on its insurance renewal, without having configured anything."**

**Gaps it leaves**
- **Session revocation on offboarding** is not stated as a requirement; a policy center that cannot instantly kill every live session for a named human is missing its most important verb.
- **No enrollment/attestation view**: "which of my 9 staff actually have MFA turned on" is the question that gets asked, and a policy center that enforces policy but cannot report coverage percentage fails the insurance use case.
- Nothing about **the Frappe/bench administrator account** — the most privileged identity in the deployment, typically shared with an MSP, and typically outside whatever policy the app enforces on itself.
- No treatment of **patient-side identity** (portal/HealthLinc auth, proxy/caregiver access, adolescent access transitions), which is a distinct and legally fraught identity surface entirely absent from this cluster.

---

## 5. Operational privacy workspace

**Verdict: valuable-enhancer — but mis-bundled, and the bundle hides the priorities.** Five unrelated capabilities of wildly different value are packed into one feat. Decomposed: **BAA/vendor registry = essential-core** (missing BAAs are a top-three OCR finding and are trivially preventable); **privacy-incident register + breach-risk worksheet = valuable-enhancer** (needed, but needed as a runbook more than a register); **access-review campaigns = questionable at nine staff** (a "campaign" for six people is a conversation); **accounting of disclosures = defer** (treatment/payment/operations disclosures are exempt, requests are vanishingly rare at this size, and the implementation cost is high). Meanwhile the single highest-frequency OCR enforcement action against small practices — **patient right of access** — is not in the bundle at all.

**Make it more useful**
- **Make the BAA registry load-bearing rather than documentary.** A connector, communications vendor, clearinghouse, processor, backup destination, or fax service cannot be *activated* without a linked, dated, non-expired BAA artifact. This converts a filing cabinet into a control: the practice cannot accidentally create an unpapered vendor relationship because the software will not let it. Add expiry tracking and 60-day renewal tasks, and auto-list every vendor the system itself talks to so the registry is pre-populated rather than authored.
- **Replace the breach-risk worksheet with a breach *runbook* with a clock.** When an incident opens, the practice needs: stop-the-bleeding steps, who to call (counsel, insurer — with the policy number stored), the four-factor risk assessment as guided questions, the 60-day notification clock counting down visibly, notification-letter templates, and the HHS portal submission checklist. A worksheet documents a decision; a runbook produces the decision under stress at 4pm on a Friday.
- **Collapse access-review campaigns into the roster.** Quarterly, one screen: nine rows, each showing person, role, last login, unused permissions, and two buttons. Signed and dated in under three minutes. That *is* the campaign, and it is the only form of it that will ever be completed.
- Keep the software firmly out of legal determination — but make it **maximally useful to the person making it**: pre-fill everything factual (what data, how many patients, which fields, encrypted or not, recipient known or unknown, ledger evidence of access) so the human's job is judgment, not archaeology.

**Alternate/better solutions**
1. **(Simpler) The Compliance Inbox.** One list. Items appear in it: "BAA with FaxVendor expires in 45 days," "Annual risk analysis due," "Q3 access review," "Restore drill overdue," "Patient records request — 12 days left." Each item has a due date, an owner, and a completion action that emits evidence. There is no privacy *workspace* — there is a to-do list that happens to constitute a compliance program, and it lives next to the practice's other work queues so it is seen daily rather than annually.
2. **Compliance calendar as subscription.** The practice's obligations are almost entirely periodic and knowable in advance. Ship a dated calendar (risk analysis, access review, restore drill, BAA renewals, policy review, HIPAA training, OCR small-breach annual filing by March 1) that generates the tasks and, when a task is completed, files the evidence itself. The office manager experiences a rhythm, not a workspace.
3. **Vendor trust inherited from the deployment.** If openPractice ships a hosted/managed option, the BAA chain for the platform, backups, and bundled connectors is pre-executed and the registry starts full rather than empty — turning the practice's hardest paperwork into a signup checkbox. Self-managed deployments get the same registry with a "you must paper this yourself" banner and a template pack.

**Reframe** — Not "operate a privacy program." Instead: **"the practice has a short list of dated obligations, each one is somebody's task, and finishing the task is what creates the proof."**

**Gaps it leaves**
- **Patient right of access is absent.** This is the most-enforced HIPAA provision against small practices by a wide margin, it has a 30-day clock, a fee cap, and a format-choice requirement, and it lands squarely in the administrative domain openPractice owns. Its omission is the single biggest content gap in this cluster.
- **No annual security risk analysis support** — the §164.308(a)(1)(ii)(A) requirement that is both mandatory and near-universally undone at this practice size, despite openPractice knowing most of the answers from its own configuration.
- **No HIPAA workforce training tracking** — required, annual, and evidence-producing; the practice will otherwise track it in a spreadsheet the auditor never sees.
- **No policies-and-procedures artifact.** OCR asks for written policies first, before any log. A generated, practice-specific, versioned policy set derived from actual configuration would be enormously valuable and is not proposed anywhere.
- **No small-breach annual reporting** workflow (the March 1 batch filing for <500-individual incidents), which is precisely the size of breach a small practice has.
- Accounting of disclosures as specified will produce a **large, low-value surface** — and the design question about distinguishing TPO from reportable disclosures is deferred to an open question rather than resolved by narrowing scope.

---

## 6. Continuous safeguard verification

**Verdict: differentiator — and the most valuable feat in this cluster for the actual threat model.** Ransomware, not improper access, is what closes small practices, and "we have backups" is a belief rather than a fact in nearly every practice of this size until the day it is tested. A product that *proves* recoverability on a schedule and hands the practice a dated attestation is doing something no incumbent meaningfully does for this segment, and it converts the vaguest of obligations into an observed fact.

**Make it more useful**
- **Restore drills must restore into a scratch environment and verify semantics, not exit codes.** Restore, then assert: patient count within tolerance, yesterday's charge total matches, a known claim renders identically, the ledger chain validates across the seam, a document opens. Then record wall-clock RTO and actual RPO measured from the restored data, not from configuration. Then destroy the scratch site. Publish the elapsed time — "your last drill took 41 minutes" is the most reassuring sentence in the product.
- **Drill the credentials, not just the data.** A restore that recovers every record but leaves stale clearinghouse SFTP keys, expired OAuth tokens, and a revoked processor key means the practice is recovered and still cannot bill. Post-restore verification must include a live-ish handshake check against every connector, or the RTO evidence is fiction.
- **Include the ransomware scenario explicitly**, because the backup that is reachable from the compromised server is the backup that gets encrypted. Verify at least one copy is offline or object-locked and *prove* immutability by attempting a delete that must fail. That failed delete is the strongest single line in any evidence pack.
- **Turn exceptions into dated, owned, escalating tasks** with a plain-English consequence: not "TLS check failed," but "patients' connection to your payment page is not secure — this is on Mike, due Friday, and it will fail your insurance renewal."
- Verify the **sensitive-field protections** by red-team assertion, not by reading configuration: attempt to retrieve an SSN through the API as a Front Desk role and require a denial; attempt a bulk export as a Biller and require the alert to fire.

**Alternate/better solutions**
1. **(Simpler) Recovery Day.** Quarterly, the software schedules a 45-minute exercise. It walks the office manager through a scripted restore in a sandbox, asks her to confirm five specific things she recognizes ("is this the balance you expect for this patient?"), times her, and prints a one-page certificate she signs and puts in the binder. It is a *human* drill with software assistance rather than an automated job with human notification — and the human having done it once is worth more, on the actual bad day, than a hundred green checkmarks.
2. **Continuous verification as a hosted control plane.** If a control plane (openPractice-operated or MSP-operated) performs the drills, holds the offsite immutable copy, and publishes the attestations, the practice's obligation collapses to "read the monthly email." Portability is preserved by making the same drills runnable on-bench; the hosted option removes the work rather than the choice. For this customer segment, this is likely the highest-value productization in the entire cluster.
3. **Fire-drill mode with a real consequence.** Once a year, deliberately break something small at a scheduled time (rotate a test credential, take a connector offline) and observe whether the practice notices and responds within the target. Measures the *human* control, which is the one that actually fails.

**Reframe** — Not "verify safeguards continuously." Instead: **"the practice knows, as a measured fact, that it can be seeing patients again by Monday — because it did exactly that in October and it took 41 minutes."**

**Gaps it leaves**
- **Upgrade safety is not covered by any feat in this cluster.** A Frappe app upgrade at a practice with no IT is a top-tier risk event; there is no pre-upgrade snapshot, no post-upgrade smoke test (one eligibility check, one claim build, one statement render, one payment authorization against sandbox), no one-click rollback, and no "your version is N-3 and unsupported" nag.
- **Sandbox vs. production drift is unaddressed**, and it cuts both ways: real PHI reaching a payer/clearinghouse test endpoint is an impermissible disclosure, and synthetic test patients reaching production claims is fraud exposure. Neither direction is blocked.
- **Configuration drift over time** has no baseline: the practice cannot see what changed in its own settings since the last evidence pack, or who changed it, so a quietly disabled control persists until the next audit.
- No verification of the things that are **outside the app but inside the risk** — workstation disk encryption, the router, the fax line, the old server in the closet. The evidence pack will imply a completeness it does not have unless it explicitly scopes itself.
- **Cadence is left as an open question**, which for this customer means it will default to never. Ship an opinionated cadence (monthly automated, quarterly human) and make changing it the configuration.

---

## 7. Auditor-ready evidence without PHI sprawl

**Verdict: differentiator, with the audience misidentified.** This is the cluster's thesis and it is a good one — but a true OCR audit of a nine-person practice is rare, while the events that *do* demand evidence happen every year: the cyber-insurance renewal questionnaire, a health plan's or hospital's vendor-security attestation, a new payer contract's security exhibit, a patient complaint, and an acquisition/merger diligence request. Aiming the pack at OCR undersells it and produces the wrong format; aiming it at the annual questionnaire makes it a feature the practice values in month three rather than in year six.

**Make it more useful**
- **Ship the pack as a PDF binder a human reads, not an export a system parses.** One page of plain-English attestations up front ("MFA is enforced for all 9 users; last verified 2026-08-24"), then dated evidence appendices. Cryptographic signatures are for the appendix; the office manager's insurance broker needs page one.
- **Generate the questionnaire answers, not just the evidence.** Map the pack's contents to the recurring questions on standard cyber-insurance applications and vendor-security questionnaires so the practice gets pre-filled answers with citations to its own evidence. This is the difference between "here is proof" and "here is the completed form."
- **Print it automatically on a schedule and tell the practice it exists.** Quarterly, unprompted: "Your Q3 trust binder is ready — 4 open exceptions." Evidence that must be requested will be requested only after it is needed.
- **Make redaction verifiable rather than promised.** Run an identifier-detection pass over every generated pack and assert zero direct identifiers; record that assertion inside the pack. A pack that leaks PHI is worse than no pack, and "we minimized identifiers" is an assurance nobody can check.
- **Show the gaps honestly.** A pack that lists what is *not* covered (workstation encryption, physical security, paper records, the EHR side of the boundary) is more credible and more useful than one implying total coverage — and the gap list doubles as the practice's improvement roadmap.

**Alternate/better solutions**
1. **(Simpler) The quarterly one-pager.** Forget packs and signatures for the beta. Every quarter, email a single page: 9 users / 9 with MFA, last restore drill and its duration, 0 open critical exceptions, 3 BAAs current and 1 expiring, last access review date, chain verified through today. Put it in a binder. This satisfies the insurance questionnaire, satisfies most vendor attestations, is comprehensible in fifteen seconds, and costs almost nothing to build.
2. **Evidence as a shareable, expiring link.** Rather than a file, a time-limited read-only page the practice sends to the insurer, the payer, or the auditor — access-logged, revocable, no PHI, and revealing precisely who looked and when. Turns diligence requests into a two-click reply.
3. **Continuous public-ish trust posture.** A per-practice trust summary the practice may voluntarily show referring providers and hospital partners ("we hold current BAAs, we drill recovery quarterly"). Converts compliance spend from pure cost into a referral-relationship asset — a genuinely differentiating move for an open-source product competing on trust.

**Reframe** — Not "generate auditor-ready evidence packs." Instead: **"the practice passes an audit — and its insurance renewal, and its hospital's vendor review — by printing a binder, having done nothing special all year."**

**Gaps it leaves**
- **No OCR-response mode.** An actual data request has a deadline, a defined scope, and a need for counsel review before release; a general-purpose pack is not that. A "we received a letter from HHS" flow that scopes, holds, and stages a response for attorney approval is missing.
- **No litigation/legal-hold concept** — the moment an incident or claim arises, retention purges must stop and be provably suspended. Purge jobs will otherwise destroy the exact evidence at exactly the wrong time.
- **Nothing covers the EHR side of the boundary.** The practice will be asked about *its* PHI, which lives in two systems; the pack must at minimum state the boundary and reference openChart's own evidence, or it will be read as covering more than it does.
- No handling of **evidence continuity across migration and upgrade** — a pack that cannot cover the period before go-live, or that changes format across versions, breaks the multi-year comparison an auditor or insurer actually performs.
- Signature verification has **no key-management story**: who holds the signing key, what happens when the server is rebuilt, and how a third party establishes that the key was ever authentic.

---

# Part 2 — Interoperability And Exchange

## 8. Contracted EHR boundary

**Verdict: essential-core.** This boundary *is* the product's identity — administrative-and-financial-only is only credible if the clinical dependency is a versioned contract rather than a coupling, and the prohibition on direct database reads is the discipline that keeps openPractice from silently becoming a bad EHR. It is also the highest-risk technical dependency in the beta, because it presumes a counterparty (openChart) whose contract maturity openPractice does not control.

**Make it more useful**
- **Define the contract as a fixture set, not a document.** Ship golden FHIR payloads plus contract tests that run against any candidate EHR and emit a plain verdict: "Your EHR supplies 7 of the 9 resources openPractice needs. Missing: DocumentReference, Encounter.class. Here is what will not work: automated charge derivation, encounter-type mapping." Compatibility becomes a five-minute pre-sales test rather than a discovery project.
- **Design for the degraded case first**, because it is the common case. Most small-practice EHRs will supply a partial or read-only surface. Every consumed resource needs a defined manual fallback path and a visible banner explaining what the practice must do by hand — otherwise a missing resource surfaces as a silently absent charge.
- **Make the boundary bidirectionally explicit in the UI.** Any clinically-owned field displayed in openPractice should be visually marked as read-only-from-EHR with a source and timestamp, and clicking it should deep-link to the EHR. Staff confusion about "which system do I fix this in" is the most predictable daily friction of a two-system design, and it is solvable with a visual convention.
- **Version the contract with a compatibility matrix the practice can see**: "openPractice 1.4 works with openChart 2.0–2.3." Upgrade coordination across two systems with no IT department requires this to be a fact, not a release note.

**Alternate/better solutions**
1. **(Simpler) Start with a demographics-and-appointments contract only, and let charges be entered by humans.** The full charge-derivation contract is where all the difficulty lives; the beta practice can enter charges from a superbill in seconds. Ship the boundary thin, prove the loop, deepen the contract in v2 once real charge patterns are observed rather than imagined. This dramatically de-risks the beta's hardest external dependency.
2. **Sync ledger with visible drift detection.** Rather than treating the EHR as a live query dependency, maintain a local projection with an explicit "last synced" state and a drift monitor that alerts when the two systems disagree about a patient's demographics or an appointment's status. Small practices tolerate slight staleness far better than they tolerate a system that is unusable when the other one is down.
3. **A no-EHR mode.** Let openPractice run standalone for practices whose EHR offers nothing usable — the boundary contract simply reports "no source" and every dependent field becomes staff-entered. Broadens the addressable beta pool enormously and forces the degraded paths to be first-class rather than afterthoughts.

**Reframe** — Not "consume versioned FHIR resources across a contractual boundary." Instead: **"staff never wonder which system to type into, and openPractice keeps working when the EHR does not."**

**Gaps it leaves**
- **No EHR outage behavior specified.** What happens to check-in, charge entry, and eligibility when openChart is down for three hours? At this size that is a whole-day operational question with no answer here.
- **The clinical→administrative correction loop is undefined.** When the EHR corrects a date of service or a diagnosis after a claim went out, openPractice must detect it, flag the claim, and drive a corrected-claim workflow. Contract consumption is specified; contract *change* consumption is not.
- **Patient identity reconciliation across the boundary** (duplicates created in one system, merges performed in the other) is a certainty at go-live and is not modeled.
- **No BAA/authority framing for the EHR link itself**, which is a PHI flow like any other and belongs in the vendor registry (feat 5).
- No statement about **which system is authoritative for the appointment**, which is the single most contested object in every PM/EHR pairing and will be a daily operational dispute if left implicit.

---

## 9. Payer channels operated as product

**Verdict: essential-core.** Eligibility, claims, remittance, and acknowledgments are the business; there is no practice-management product without them, and "operated as product" — shared enrollment, credential, validation, retry, and exception handling rather than four bespoke integrations — is exactly the right framing. The differentiating claim is modest but real: incumbents make 277/999 outcomes semi-opaque and small practices routinely lose claims in the acknowledgment gap.

**Make it more useful**
- **Treat trading-partner enrollment as a tracked workflow, not a prerequisite.** Payer EDI enrollment is weeks of paper per payer per transaction type and is the number-one go-live delay in this segment. Model enrollment state per payer per transaction (not started / submitted / pending / approved / rejected), with the forms, the dates, the contact, and a projected go-live. Then the implementation plan is visible instead of mysterious.
- **Translate every acknowledgment into English and into a task.** A 999 rejection or a 277CA A3 code must render as "Blue Cross rejected this claim before adjudication because the subscriber ID format is wrong — fix and resubmit," assigned to a person. Raw code display is the incumbent behavior worth beating.
- **Never let a claim be in an unknown state.** Every submitted claim carries an expected-acknowledgment clock; missing a 999 within its window is itself an exception. Silence is the failure mode that costs small practices real money, and it is invisible in every system that only reports what arrived.
- **Make the eligibility response *decision-shaped*.** Nobody wants a 271 dump; they want "active, $40 copay, $1,200 deductible remaining, this service needs prior auth." Parse to decisions, keep the raw payload for provenance, show it only on demand.
- **Bidirectional test-vs-production isolation** (see the missing-feat list): production credentials must be structurally incapable of receiving synthetic patients and test endpoints structurally incapable of receiving real ones.

**Alternate/better solutions**
1. **(Simpler) One clearinghouse, deeply, for the beta.** Resist the connector-neutral abstraction as *shipped surface* while keeping it as internal structure. One partner, integrated to the point of excellence — enrollment help, real acknowledgment parsing, tested remit posting — beats three partners at 70%. The state machine stays connector-neutral internally so portability is preserved; the practice sees one working channel.
2. **Payer channels as a service tier.** Offer a managed EDI option where openPractice (or a partner) holds the connectivity and enrollment burden, while the on-bench path remains fully functional. Small practices consistently pay to make this problem disappear, and it funds the open core.
3. **Claim status by polling as a safety net.** Even with good 277 handling, add a low-frequency 276 sweep for claims quiet past a threshold. Redundant, cheap, and it converts "lost claim discovered at 90 days" into "flagged at 14 days" — a directly monetizable outcome that no amount of console polish achieves.

**Reframe** — Not "operate payer transaction channels." Instead: **"no claim is ever in a state nobody knows about, and every payer rejection arrives as a sentence and a task."**

**Gaps it leaves**
- **Credential expiry and rotation is unowned.** Clearinghouse SFTP passwords, payer portal logins, and SFTP host keys expire on payer-driven schedules; expired credentials are the leading cause of small-practice EDI outages and nothing here tracks expiry, owner, or renewal.
- **No payer-side maintenance calendar** — planned clearinghouse and payer downtime windows arrive by email to an address nobody watches, and the resulting failures read as bugs.
- **Prior authorization is absent from the channel list** despite CMS's 2026/2027 API mandates on impacted payers, flagged in the beta overview but not reflected here.
- **No claim-volume anomaly detection**: "you normally send 60 claims a week, you sent 4" is a better outage detector than any health check, and it is the failure this practice will otherwise discover at month-end.
- **X12 implementation-guide licensing** constrains what can ship in an open-source repo; the tension is acknowledged in prose but there is no proposed distribution model.

---

## 10. Coexistence and migration kits

**Verdict: essential-core for the beta specifically — and under-scoped as written.** The beta practice is switching *from* something, and migration is the highest-probability cause of beta failure: bad balances destroy trust in the financial system permanently and in week one. But scoping the kit to OpenEMR is likely wrong for the actual design partner, who is far more likely to be leaving Tebra/Kareo, eCW, AdvancedMD, or a legacy desktop system — none of which offer clean declared exports.

**Make it more useful**
- **Design for CSV-and-a-shrug as the primary input.** The realistic migration substrate is a pile of exported spreadsheets of uneven quality plus a read-only copy of an old database. Build a general mapping-and-reconciliation engine with profiles per source, rather than a source-specific extractor; the OpenEMR profile is then one instance, not the architecture.
- **Reconcile to money, and make the practice sign the number.** The go/no-go artifact is a one-page reconciliation: total patient AR before and after, total insurance AR before and after, count of open claims, count of credit balances, top 20 largest balances listed individually for eyeball verification. The office manager signs it. That signature is the cutover gate and the most valuable evidence artifact in the whole project.
- **Migrate rejects into a work queue, not a report.** Whatever fails to map becomes assigned, dated work items in the live system, so the practice starts day one with a visible, shrinking backlog rather than a silent data hole.
- **Rehearse at least twice with published deltas**: each dry run reports what changed since the last one, so the practice can see convergence. Blind repeated rehearsal is how migrations lose weeks.
- **Define the legacy read-only period explicitly** — who retains access to the old system, for how long, under what BAA, with what audit — because it is a live PHI surface for years and is otherwise forgotten the day after cutover.

**Alternate/better solutions**
1. **(Simpler) Migrate open items only; archive the rest.** Bring across active patients, open balances, and open claims. Everything historical stays in a read-only archive (or the legacy system, or a PDF-per-patient dump) accessible by search. Cuts migration scope by an order of magnitude, removes the largest class of reconciliation error, and matches what staff actually need on day one. Full history migration is a v2 problem for practices that demand it.
2. **Parallel run with a divergence report.** Run both systems for two to four weeks on new activity only, and produce a daily "these 6 things disagree" list. Converts cutover from a leap into a monitored convergence, which is dramatically less frightening for a practice betting its cash flow on a beta.
3. **Migration as a hosted service with a fixed-price guarantee.** For this segment, the winning offer is not a toolkit — it is "send us your export, we hand you a reconciled site and a signed balance sheet." The kit exists to make that service cheap to deliver; the practice never sees a mapping template.

**Reframe** — Not "extract and reconcile legacy data through declared exports." Instead: **"on Monday morning the balances are right, the office manager already checked and signed for them, and anything that did not come over is on somebody's list."**

**Gaps it leaves**
- **Migrated data has no ledger provenance** — see feat 2. The evidence position for pre-go-live history is undefined.
- **No rollback plan for a failed cutover.** If Wednesday goes badly, what is the path back? Without one, the practice is committed by 9am regardless of outcome, which is the actual nightmare scenario.
- **Legacy PHI handling at end of life** — secure destruction, retention obligations on the old system, the vendor's data-return clause — is a compliance event with no home in this cluster.
- **No mapping of legacy users/permissions to new roles**, so day one either over-grants everyone or blocks half the staff.
- **Nothing about the payer-side identifiers** (submitter IDs, payer-assigned provider numbers) that must move with the data or claims fail immediately after cutover.

---

## 11. Bounded automation subscriptions (MedxFactory scopes)

**Verdict: defer.** This is architecture-driven rather than customer-driven: a nine-person practice's first beta has no consumer for revocable webhook scopes, and building a subscription-authority model for a sibling product before the sibling product's use cases exist is speculative surface area on the critical path. The *principle* — no blanket access, no default financial authority — belongs in the constitution and in the credential model; the *feature* can wait.

**Make it more useful** (if built anyway)
- Fold it into one **machine-identity object** shared with API clients, connector credentials, and integration users (feat 12), so the practice sees a single "what systems can touch our data" list rather than two parallel concepts with different vocabularies.
- **Express scope in outcomes the office manager can judge**: "can read appointment times," "can send patient texts," "can never see balances," "can never move money." Nobody at this practice can evaluate `appointment:read`, and an approval nobody understands is not an approval.
- **Default every subscription to expire.** 90 days, renewal is one tap with a usage summary ("used 12,400 times, last used yesterday"). Unused subscriptions die on their own, which is the only reliable form of revocation at this scale.
- Make every automated action **visibly attributed in the record's history as a machine actor** with its subscription name, so staff can see that the system, not a colleague, sent that message.

**Alternate/better solutions**
1. **(Simpler) No subscriptions; a signed capability list per integration.** Each integration is installed with a fixed capability set declared at install time and changeable only by reinstall. No runtime scope negotiation, no revocation UI, no expiry engine — just an install screen that says what it will do and a remove button. Comprehensible, auditable, and roughly a tenth of the work.
2. **Consequential actions require human confirmation regardless of scope.** Rather than governing what automation *may* do, govern what it may do *unattended*: anything touching money, patient contact, or authority produces a proposed action in a human queue. Scope becomes far less load-bearing when nothing irreversible is autonomous, which is the constitution's stance anyway.
3. **Automations as employees.** Model each automation as a staff-roster entry with a job (role recipe), a supervisor, a start date, and an end date. It inherits the entire access, review, offboarding, and audit apparatus already built for humans, and the office manager manages it with a mental model she already has. This is a genuinely better abstraction for this customer than a scope model.

**Reframe** — Not "grant revocable capability scopes to automation." Instead: **"every robot working here is on the staff list, has a job title, and can be fired the same way a person can."**

**Gaps it leaves**
- **No secret rotation story** for subscription credentials, and no behavior on rotation failure.
- **No blast-radius limit**: a compromised or buggy subscription with valid scope can still act at machine speed. Rate limits are mentioned for the public API but not as a safety property here.
- **Nothing about liability and BAA** when an automation acts — MedxFactory processing PHI is a business-associate relationship requiring its own paper, and the registry link is not made.
- **No kill switch.** The practice needs one visible button that stops all automation immediately when something is behaving strangely at 2pm on a Tuesday.

---

## 12. Versioned public surface (API keys, OAuth clients, deprecation windows)

**Verdict: defer as a *public* surface; promote its credential-lifecycle half to essential-core.** There are zero third-party developers at beta, and deprecation windows, quotas, and additive version coexistence are costs paid for an ecosystem that does not exist yet. But the practice absolutely has machine credentials on day one — clearinghouse, processor, EHR, communications vendor, backup destination — and *those* need owners, expiry, rotation, and revocation immediately. The feat has the right mechanics attached to the wrong population.

**Make it more useful**
- **Retarget from "public API consumers" to "every credential this practice depends on."** One inventory: what it is, which vendor, who owns it, when it expires, when it was last rotated, whether a BAA covers it, and what breaks if it dies. This is the object the practice actually needs and does not have anywhere else in the cluster.
- **Expiry is a countdown with a task, not a field.** Sixty days out, a task appears with renewal instructions specific to that vendor. Credential expiry is one of the top causes of small-practice integration outages and it is entirely preventable with a calendar.
- **Rotation must be a rehearsed, reversible, two-window operation** (new credential accepted before old is revoked), because a rotation that breaks claims submission on a Thursday afternoon is worse than the risk it mitigated. Ship a "test this new credential before switching" step.
- Keep versioning discipline **internal**: version the contracts, keep the migration machinery, and simply do not publish a developer program yet. Nothing is lost and the support surface stays at zero.

**Alternate/better solutions**
1. **(Simpler) A vault page, not an API program.** One screen listing every secret with status lights and expiry dates, plus "test connection" per row. That is the whole feature for beta, and it is worth more to the practice than an entire developer portal.
2. **Credentials as a hosted concern.** In a managed/control-plane deployment, the platform holds and rotates connector credentials on the practice's behalf and the practice never sees a key. Self-managed sites get the vault page. Removes the highest-toil, highest-expertise item from the office manager's plate entirely.
3. **Read-only reporting API first.** If any external surface ships in beta, make it the one with no blast radius — a scoped read API for the practice's accountant or consultant, no writes, no PHI beyond financial summaries. Exercises the versioning machinery against a real, low-risk consumer.

**Reframe** — Not "govern the lifecycle of a public API surface." Instead: **"nothing the practice depends on ever expires without warning, and nobody has to know what an OAuth client is."**

**Gaps it leaves**
- **No emergency revocation drill.** If a credential is suspected compromised at 4pm, what is the sequence, who does it, and what breaks? Untested revocation is not a control.
- **No handling of credentials held outside the app** — the vendor portal logins the office manager keeps in a notebook — which is where most of them actually live.
- **Offboarding intersection:** credentials personally created by or known to a departing employee need forced rotation, and no feat connects those two events.
- **TLS certificate expiry** is arguably the highest-frequency expiry event of all and belongs in the same inventory rather than in the separate safeguard-verification feat.

---

## 13. Interface operations console

**Verdict: questionable as a console; essential as a status surface.** "Dead letters," "replay eligibility," "schema failures," and "acknowledgment interpretation" are integration-engineer vocabulary presented to a person who does not have that job and will not acquire it. The underlying need — knowing when something is broken and what to do — is unambiguously essential; the console framing guarantees the screen is opened only by developers, which means the practice learns about outages from a payer's phone call.

**Make it more useful**
- **Replace the console with two things: a status line and a work queue.** The status line is consumer-grade and lives where staff already are — one row, plain language, green or amber: "Insurance checks: working. Claims: working. Payments: working. Statements: 3 not delivered." The work queue holds anything requiring a human, expressed as tasks with owners and due dates, alongside the practice's other work. No new destination, no new vocabulary.
- **Alert on silence, not just on errors.** The dangerous failure is the quiet one: no remits for six days, eligibility volume down 80%, zero acknowledgments since Tuesday. Volume-anomaly detection against the practice's own baseline is worth more than every error dashboard combined and requires almost no domain knowledge to implement.
- **Every failure carries a business consequence and a fallback**, in that order: "12 claims did not go out yesterday (about $4,300). We will retry automatically at 6pm. If it fails again you can submit these through the payer portal — here is the list." Never present a technical condition without stating what it costs and what the human can do instead.
- **Make replay safe by construction and phrase it as an intent**, not an operation: the button says "Try sending these again" and the system handles idempotency, duplicate-claim risk, and non-idempotent payment posting internally by refusing or requiring confirmation. Never expose replay eligibility as a judgment the office manager must make.
- **Keep the engineer's console** — but as a hidden diagnostic view for the MSP or the openPractice support engineer, explicitly not part of the practice's product surface.

**Alternate/better solutions**
1. **(Simpler) A daily 7am email and nothing else.** "Everything is working" or "2 things need attention, here they are." No console, no dashboard, no navigation item. For a practice whose integration surface is four connectors, a daily digest plus an exception alert is complete coverage, and it is read on a phone before the office opens.
2. **Integration health as a consumer status page.** Model it on the status pages the office manager already understands from her bank and her internet provider: big colored dots, plain names, an incident history, and "we are aware and working on it" language written by the system itself. Nothing about queues or schemas is exposed.
3. **Self-healing with an audit trail.** For the majority of transient failures, retry with backoff and never tell the practice at all — only surface what survived automated recovery. Then report monthly: "47 problems occurred, 45 fixed themselves, 2 needed you." This builds trust in the automation *and* provides the evidence that the automation works.

**Reframe** — Not "provide visibility into channel health, dead letters, and replay." Instead: **"nothing is stuck; and if something is, the practice already knows, knows what it costs, and knows who is fixing it."**

**Gaps it leaves**
- **No escalation path when the practice cannot fix it.** With no IT department, "contact your administrator" is a dead end. The design needs a defined route to a support human or MSP, with a diagnostic bundle attached automatically.
- **No partner-incident intake**: when the clearinghouse has an outage, the practice should see it in the same place rather than discovering it by ticket.
- **No degraded-mode operating guidance** — the printable "what we do when claims are down" card that lets the front office keep working without improvising.
- **Monitoring is silent about its own health**: if the alerting job dies, the status stays green forever. A heartbeat/dead-man's-switch is required or the entire surface is unfalsifiable.
- **PHI-minimized diagnostics** are asserted but not designed; the moment support needs to debug a real 837, the minimization policy will be tested and there is no defined path for authorized, logged, time-boxed payload access.

---

## 14. End-to-end transaction trace (correlation identity)

**Verdict: differentiator.** A single identity linking eligibility → appointment → charge → claim → acknowledgment → remittance → posted payment is cheap to establish at build time, effectively impossible to retrofit, and it is the mechanism behind the two questions that consume the most staff time in a small practice: "why hasn't this paid?" and "where did this number come from?" It also quietly underwrites the audit ledger, the evidence packs, and the operations queues, which makes it structurally load-bearing well beyond its own feat.

**Make it more useful**
- **Surface it as a patient-visit timeline, not a trace.** One vertical list on the patient/visit screen: verified insurance Tuesday, seen Thursday, claim sent Friday, payer acknowledged Monday, paid $84 on the 14th, patient owes $36, statement sent. That is a trace rendered as a story, and it is simultaneously the staff tool, the patient-service tool ("let me see what happened with your bill"), and the debugging tool.
- **Extend correlation past the money boundary to patient communications.** Every text, statement, portal notification, and reminder tied to the same identity — otherwise a privacy complaint about a message and a question about a claim require two different tools and cannot be answered together.
- **Handle the partner-strips-the-ID case explicitly rather than as an open question.** Maintain a correspondence table keyed on whatever the partner *does* echo (control numbers, claim IDs, check/EFT trace numbers, payer claim numbers) and treat re-association as a first-class, testable function with a measurable re-association rate. This is the trace's hardest engineering problem and the docs currently defer it.
- **Publish an explicit "unknown" state.** When a step's outcome cannot be correlated, say so in the timeline rather than showing a gap. Visible unknowns get worked; invisible gaps get discovered at 90 days.

**Alternate/better solutions**
1. **(Simpler) A per-visit thread.** Skip distributed-tracing framing entirely; every financial event simply carries a visit ID and renders in one chronological thread. Ninety percent of the value, a fraction of the machinery, and a mental model any staff member already has from email.
2. **Trace-driven exception generation.** Rather than a queryable trace nobody queries, define expected next steps with deadlines at each stage (claim sent → 999 within 1 day → 277CA within 3 days → remittance within 30). Any timeline that stalls generates work automatically. The trace becomes a proactive engine instead of a forensic tool, which is a much larger behavioral difference than it sounds.
3. **Patient-facing provenance.** Let the patient see a plain-language version of their own timeline ("your insurance was billed on the 3rd; they paid on the 14th; this is your remaining share"). Billing-question call volume is a real cost at this size, and no incumbent does this well.

**Reframe** — Not "propagate a correlation identity across all adapters." Instead: **"anyone can answer 'what happened with this visit's money' by reading one screen top to bottom."**

**Gaps it leaves**
- **No retention rule for trace data**, which is high-volume and PHI-adjacent; it will grow without bound on a small practice's server.
- **Correlation across the migration seam** is undefined — pre-go-live claims have no trace and will render as empty timelines unless explicitly modeled as "before openPractice."
- **No cross-boundary correlation with the EHR**, so "the visit" is traceable financially but not to the clinical event that generated it, weakening charge-derivation disputes.
- **Correlated logs are a PHI aggregation surface** in their own right — a trace ID that resolves to a patient's full financial history is exactly the object that must not appear in a diagnostic bundle sent to support, and the redaction interaction is unaddressed.

---

# Part 3 — Synthesis: Platform And Trust

## 15. Trust as shipped control plane

**Verdict: essential-core** as a product posture — "ships working, not ships configurable" is the correct and differentiating stance against both OpenEMR (assembly required) and the cloud incumbents (trust us, no visibility). The load-bearing and understated half is "operate identically across Flutter, Desk, and APIs": Frappe's Desk exposes a wide, powerful, developer-shaped surface, and enforcing minimum-necessary uniformly across a hand-built app, a generic admin UI, and an API is genuinely hard engineering that this bullet compresses into one clause.

**Make it more useful**
- **Decide whether the practice ever sees Desk at all.** The most defensible answer for this customer is no: Desk is disabled for practice staff and reserved for support/MSP identities. This eliminates an entire class of enforcement risk and an entire class of "the office manager broke something in a doctype" incidents, and it should be the shipped default rather than a hardening step.
- **Make "shipped" verifiable**: a startup self-check that asserts the control plane is intact (MFA enforced, encryption on, ledger chaining active, backups configured and verified, no default credentials) and refuses to declare the site production-ready until every check passes. Secure-by-default only means something if the deviation is detected.
- **Enforce at the data layer, not per-surface.** Row/field authority evaluated once, below every entry point, is the only architecture that makes the "identically across surfaces" claim true; per-controller checks will drift within two releases.
- **Publish the control plane's own threat model and its limits.** For an open-source trust product, saying clearly what it does *not* protect against (a compromised server admin, physical access, the practice's own workstations) is a credibility asset, not a weakness.

**Alternate/better solutions**
1. **(Simpler) One security posture, no dials.** Ship a single hardened configuration with no security settings exposed at all in beta. Every dial is a support question, a drift vector, and an audit finding. Add configurability only when a real customer is blocked by its absence.
2. **Hosted control plane, portable data plane.** The practice's site runs anywhere; the *trust functions* (attestation collection, offsite immutable backup, drill scheduling, evidence generation, credential expiry monitoring, anomaly alerting) are optionally operated centrally. This is the highest-leverage structural idea available to this cluster: it makes self-managed compliance largely unnecessary without making self-management impossible, and it directly answers "no IT department" rather than mitigating it. Constitution Principle VI is satisfied so long as every function remains runnable on-bench.
3. **Certified deployment recipes.** Rather than a control plane the practice operates, publish a small number of blessed deployment shapes (single-server bench, MSP-managed, hosted) each with an automated conformance check that emits a signed "this deployment matches recipe X as of today." Trust becomes an attribute of the recipe, and drift from it becomes the only thing anyone has to monitor.

**Reframe** — Not "ship a portable HIPAA control plane." Instead: **"there is no security configuration, because there was never a decision to get wrong."**

**Gaps it leaves**
- **The Frappe/bench administrator is outside the control plane** — the most privileged identity in the system, frequently shared with whoever set up the server, and unaddressed by any feat here.
- **No supply-chain position**: an open-source product's app and dependency updates are a trust surface (signed releases, pinned dependencies, a security-advisory feed the practice actually receives), and none of it appears.
- **Upgrade safety again** — a control plane that is correct at install and unverified after every subsequent upgrade decays silently.
- **No multi-tenancy or MSP story**, though "one practice per site" plus MSP-managed operation is the stated beta hypothesis; an MSP managing eight sites needs cross-site identity and evidence aggregation that nothing here provides.

---

## 16. One governed integration spine

**Verdict: essential-core.** The unification is the actual value — shared credential lifecycle, retry policy, monitoring, replay semantics, and error vocabulary across EHR, payer, migration, and automation channels — because the alternative is four bespoke integrations with four failure vocabularies, which is precisely the incumbent condition. This is an architectural constraint worth defending in review even when a single connector would be faster to special-case.

**Make it more useful**
- **Publish the connector contract as a real, testable interface** with a conformance suite, so a new connector is a two-week job for a community contributor rather than a core-team project. For an open-source product, the spine's strategic value is that other people can extend it safely; that only happens if conformance is executable.
- **Standardize the failure taxonomy across all channels** — auth, config, transport, schema, business-rule, partner-outage — because it lets one status surface, one alert policy, one retry policy, and one set of staff-facing sentences cover every connector, present and future. This is the highest-leverage detail in the entire spine.
- **Make idempotency a declared property of every operation** in the contract, not a per-connector convention. Replay safety is asserted repeatedly across these documents and is only real if the spine enforces it structurally.
- **Include a manual connector.** Fax, paper, and payer-portal work are real channels at this practice size; modeling them inside the spine (with tasks, evidence, and correlation, just without automation) means the trace and the status surface stay complete rather than silently ending wherever automation ends.

**Alternate/better solutions**
1. **(Simpler) Two connector tiers.** Tier 1 (EHR, clearinghouse, processor) gets the full spine. Tier 2 (everything else) gets a deliberately dumb webhook/CSV path with no bespoke engineering. Prevents the spine's generality budget from being spent on the long tail during beta.
2. **Spine as an event log, not a framework.** Every external interaction is an append-only event with a correlation ID and a status; connectors are thin producers/consumers. This unifies the spine with the audit ledger (feat 2) — one durable timeline instead of two — which is a materially simpler and more coherent architecture than parallel audit and integration subsystems that must later be reconciled.
3. **Adopt an existing integration engine as an optional backend** (Mirth or similar) for exotic channels while keeping first-party paths for the core four. Reduces the "we must build every adapter" burden without making an external engine mandatory.

**Reframe** — Not "govern all integrations through one spine." Instead: **"every way data enters or leaves this practice fails the same way, is named the same way, and is fixed the same way."**

**Gaps it leaves**
- **No connector versioning or compatibility matrix** — when a payer changes a companion guide or the EHR upgrades, the practice needs to know before it breaks.
- **No sandbox model in the spine itself**, so test-vs-production isolation is left to each connector to get right (see missing feats).
- **No shared rate-limit/backpressure policy**, so one misbehaving connector can starve the others on a small single-server deployment.
- **No cost/quota visibility** — eligibility and status transactions often carry per-transaction fees, and a retry storm is a bill as well as an outage.

---

## 17. Correlation as first-class data

**Verdict: differentiator — and a duplicate that should be resolved.** This is feat 14 restated at synthesis level; the useful distinction is that the synthesis should *own* the correlation schema as a cross-cutting contract (audit ledger, integration spine, operations queues, and reporting all bind to it) while the atomic owns its payer-channel propagation. Left as two feats with the same name, they will be implemented twice with subtly incompatible semantics.

**Make it more useful**
- **Declare the correlation identity a versioned public contract** with a written specification: what it identifies (a visit's administrative-financial thread), when a new one is minted, how child operations inherit, how re-association works when a partner strips it, and how it appears in logs, evidence packs, and exports. Everything else in the cluster binds to it, so ambiguity here is expensive.
- **Bind the audit ledger to the same identity** so a privacy investigation and a claim investigation read the same timeline. This is the specific joint the synthesis claims and it should be an explicit schema requirement, not an aspiration.
- **Make it queryable by staff without a query language** — search by patient, date, claim, check number, or amount and land on the same timeline. If reaching a trace requires SQL, the practice has OpenEMR's analytics problem with extra steps.
- **Measure correlation coverage as a product health metric**: percentage of operations successfully correlated end to end. A number that can regress is a number that gets defended.

**Alternate/better solutions**
1. **(Simpler) The visit is the correlation ID.** Nearly every administrative-financial thread in an ambulatory practice belongs to one encounter. Use the encounter/visit identity, accept that non-visit threads (credit balances, refunds, patient-level statements) are the exceptions, and handle those explicitly. Removes a whole abstraction layer and matches how staff already think.
2. **Correlation as provenance links rather than a shared token.** Each record points at what produced it; the "trace" is a graph walk. More robust when partners strip identifiers (the links survive independent of what the partner echoes) and it composes naturally with the financial-provenance claims made in the revenue-cycle cluster.
3. **Two-level identity: thread + hop.** A durable practice-side thread ID plus per-hop external references stored in a correspondence table. Explicitly separates "our identity" from "whatever the partner gave back," which is exactly the ambiguity that makes re-association hard, and it makes the re-association rate measurable.

**Reframe** — Not "propagate correlation identity as first-class data." Instead: **"one thread per visit, and everything that ever happens about that visit's money hangs off it."**

**Gaps it leaves**
- **No governance of the identity across system boundaries** — what openPractice sends to a partner may be logged in the partner's systems, so the identity must be non-PHI-bearing and non-guessable; nothing states this.
- **No merge/split semantics** when patients are merged, visits are combined, or a claim is split, all of which are routine and all of which fracture a naive trace.
- **Overlaps unreconciled with revenue-cycle provenance claims** in the sibling cluster, which asserts a similar end-to-end financial chain; one of the two must be authoritative or both will be half-built.

---

## 18. Evidence generated, not assembled

**Verdict: differentiator — the cluster's thesis, and the right one.** Compliance-as-byproduct is genuinely how a nine-person practice can hold a defensible position, and it is a claim no incumbent makes credibly for this segment. Its risk is entirely in the last mile: evidence that is generated but never opened, formatted for a machine, or signed with a key no third party can validate is indistinguishable from no evidence at all when the questionnaire arrives.

**Make it more useful**
- **Push the artifact, on a schedule, to a human.** The pack must arrive quarterly by email, unrequested, with a one-line summary and an exception count. Generation without delivery is the whole failure mode.
- **Close the loop from evidence to behavior**: if the pack shows a gap, the pack should *create the task that fixes it*. Otherwise the practice accumulates a beautifully documented record of things it never did.
- **Make evidence quality visible.** Grade coverage honestly — "12 of 15 controls evidenced by system observation, 2 by attestation, 1 not covered" — because a pack that overclaims is a liability in the exact moment it is relied upon.
- **Design for the three real consumers explicitly**: the insurance broker (needs page one), the payer/hospital vendor reviewer (needs a questionnaire mapping), and counsel responding to OCR (needs raw dated evidence and a legal hold). One generator, three renderings.
- **Prove the "no PHI" claim with an automated identifier scan on every generated pack**, recorded in the pack itself.

**Alternate/better solutions**
1. **(Simpler) The binder.** A physical or PDF binder with dated tabs, regenerated quarterly, that the practice keeps on a shelf. Low technology, high adoption, and it matches exactly what a small practice already does with its OSHA and CLIA paperwork. This is very likely the correct beta implementation of the entire evidence concept.
2. **Continuous attestation feed.** Instead of periodic packs, maintain a running, always-current trust state that can be rendered at any moment for any audience. Removes the "is the pack stale" question and makes evidence a property of the system rather than an event.
3. **Third-party-verifiable anchoring.** Publish only the daily hash to a location the practice does not control (its CPA's mailbox, an object-lock bucket, a transparency log). A third party can then verify that the evidence existed on the claimed date without ever seeing any content — which is precisely the property an auditor cares about and one that a self-signed pack does not have.

**Reframe** — Not "generate signed compliance evidence packs from operational data." Instead: **"the practice's proof is a quarterly binder nobody had to prepare, and every page in it is the exhaust of work someone had to do anyway."**

**Gaps it leaves**
- **No key management for the signatures**, so the strongest technical claim in the cluster rests on an undefined foundation.
- **No handling of the period before the system existed** — the practice's first-year pack covers three months and the auditor asks about three years.
- **No evidence about the humans**: training completion, policy acknowledgment, and background checks are what OCR asks for first and none of it is produced by normal system operation unless the system is asked to hold it.
- **Nothing anchors the pack to a risk analysis**, which is the document every other document is supposed to reference; without it the pack is a pile of controls with no stated rationale.

---

# Cluster-level review

## Missing feats

- **Same-Day Offboarding Switch** — one action ("Mike's last day is today") that kills live sessions, revokes tokens and API keys he created, forces rotation of shared credentials he had access to, reassigns his open work and queue items to a named person, removes him from alert routing, and files a dated termination-of-access record into the evidence pack. This is the single highest-frequency real HIPAA access failure at this practice size and no proposed feat performs it.

- **Right of Access Clock** — patient (and personal-representative) record and billing-record requests as a tracked workflow: identity verification, scope selection, format and delivery-channel choice, the fee-limit guardrail, a visible 30-day countdown with the one permitted extension, PHI-safe delivery, and a completion receipt. This is the most-enforced HIPAA provision against small practices, it is squarely administrative, and it is entirely absent from the cluster.

- **Annual Risk Analysis On Rails** — generate 70–80% of the required §164.308(a)(1)(ii)(A) security risk analysis from live system configuration and observed operation, then ask the human twelve questions about paper, premises, workstations, and vendors. Produces a dated, versioned, year-over-year-comparable document that almost no practice of this size currently has and every enforcement action asks for first.

- **BAA-Gated Vendor Activation** — no connector, communications vendor, clearinghouse, processor, fax service, or backup destination can be switched on without a linked, dated, unexpired BAA artifact; expiry generates renewal tasks 60 days out. Converts vendor paperwork from a filing habit into an enforced precondition that cannot be forgotten.

- **Credential and Certificate Expiry Radar** — one inventory of every secret, key, certificate, license, and portal login the practice depends on, each with an owner, an expiry date, a "what breaks if this dies" note, a test-before-switch rotation flow, and a renewal task on a countdown. Expired credentials are the leading cause of small-practice integration outages and currently have no home.

- **Breach Response Runbook With A Clock** — when an incident opens: containment steps, who to call (with counsel and cyber-insurance policy details pre-stored), the four-factor risk assessment as guided questions with facts pre-filled from the ledger, a visible 60-day notification countdown, notification-letter templates, the HHS portal checklist, the annual small-breach March 1 filing, and an automatic legal hold that suspends retention purges. A worksheet documents a decision; this makes the decision under stress.

- **Recovery Day** — a scheduled quarterly 45-minute human exercise: restore into a scratch environment, verify five recognizable facts, verify the connector credentials still work, prove the immutable copy cannot be deleted, record wall-clock RTO, print a certificate the office manager signs. The practice's ransomware survival depends on having done this once, not on having configured it.

- **Upgrade Safety Rail** — pre-upgrade snapshot, a post-upgrade smoke suite that exercises one eligibility check, one claim build, one statement render, and one payment authorization against sandbox, an automatic rollback on failure, and a configuration-drift report showing what changed and who changed it. A practice with no IT cannot be the safety net for its own upgrades.

- **Test/Production Firewall** — structural, bidirectional isolation: real patient data can never reach a sandbox endpoint (impermissible disclosure) and synthetic test patients can never reach a production claim, payment, or patient-communication channel (fraud and patient-harm exposure). Enforced at the spine, visible as a mode banner, impossible to defeat by configuration.

- **Silent Failure Detector** — baseline the practice's own normal volumes (claims sent per day, remits received per week, eligibility checks per session, statements delivered) and alert on the absence of expected activity, not merely on errors. Catches the outages that error monitoring structurally cannot see, which are the expensive ones.

- **Compliance Inbox** — one dated, owned to-do list holding every periodic obligation (risk analysis, access review, restore drill, BAA renewals, training, policy review, small-breach filing) where completing the task is what emits the evidence. Replaces the "privacy workspace," the "review campaigns," and most of the evidence-pack ceremony with a queue the office manager already knows how to work.

- **Support Access With Consent** — a defined, time-boxed, consent-gated, fully-logged path for an openPractice or MSP engineer to access the practice's site, with an automatic diagnostic bundle that is PHI-scrubbed by construction. Without this, real support happens over screen-share with the office manager's credentials, which quietly voids the entire access-control position.

## Cross-feat gaps (the seams)

- **The ledger records access but cannot prove absence of access.** Breach risk assessment needs "nobody outside TPO touched this," which requires *completeness* assurance (sequence continuity, gap detection per source), not just tamper-evidence. The security doc designs integrity; the privacy doc consumes a property integrity does not provide.

- **Append-only integrity and retention purge are in direct, unacknowledged conflict.** Record-retention limits, minors' rules, and data minimization all require deletion from a structure defined as immutable. No feat proposes hash-preserving tombstones or chain-safe redaction, and this will surface as a hard blocker rather than a design choice.

- **Break-glass and machine credentials are declared "the same object class with different reviewers" — but the practice has only one reviewer, and she cannot evaluate a machine scope.** The synthesis's most elegant joint assumes an organizational depth that does not exist at nine people. Either the machine side needs a different (automated, expiry-driven) governance model or the review is theater.

- **The BAA registry and connector activation never touch.** Feat 5 files vendor paperwork; feats 9, 12, and 16 activate vendors. Nothing prevents an unpapered PHI flow, which is the exact failure the registry exists to prevent.

- **Restore drills verify data but not the ability to operate.** After a real restore, OAuth tokens, SFTP keys, and processor credentials may be stale or rotated. An RTO measured on data recovery alone is a number that will be wrong on the day it matters, and the credential inventory (missing entirely) is the missing input.

- **Correlation stops at the money boundary.** Patient communications, statements, and portal activity are outside the trace, so a privacy complaint about a text message and a question about a claim require different tools and cannot be answered on one timeline — which defeats the synthesis's own "single-query answers" claim.

- **Migration is a second PHI surface with no trust plane.** Reading a legacy system's data involves a vendor relationship, credentials, exports living on someone's laptop, and a years-long read-only retention obligation. None of it appears in the security doc, and none of the migrated history carries ledger provenance.

- **Code-set loads (CPT/HCPCS/ICD) are treated purely as an interop concern** though they are a configuration change with financial blast radius, a licensing constraint, and an upgrade-safety event. They belong under the same change-control and rollback machinery as an app upgrade.

- **Evidence packs assume a signing key whose custody, rotation, rebuild survival, and third-party verifiability are undefined**, which makes the strongest claim in the cluster the least substantiated one.

- **Both clusters assume the practice can escalate.** Break-glass review, dead-letter triage, replay decisions, credential rotation, and incident response all implicitly assume a second competent human. In the target customer there frequently is not one, and the missing **Support Access With Consent** feat is the seam that makes the rest of the design operable.

## Intuitiveness assessment

**Can a non-technical office manager operate this trust plane?** As currently framed, no — not because the capabilities are wrong, but because the surfaces are named after the concepts rather than the work. "Minimum-necessary authority designer," "interface operations console," "accounting of disclosures," "dead letters," "replay eligibility," and "access-review campaigns" are all vocabulary from jobs this practice does not employ. The reliable test: if a screen's name would not appear in a sentence the office manager says out loud, she will not open it, and every downstream benefit that depends on her opening it does not exist.

**What must be invisible** — integrity chaining, correlation identity, encryption verification, session policy enforcement, idempotency and replay semantics, schema versioning, scope negotiation, key rotation, retry and backoff, dead-letter mechanics, redaction, and every word of X12 and FHIR. These should be observable in a support view and never in the practice's navigation.

**What must be visible** — four things, and ideally on one page called something like *Practice Health*, sitting beside the practice's other daily work rather than in a compliance section:

1. **Who works here** (the staff roster — the entirety of access administration, including the offboarding switch)
2. **What needs attention** (the Compliance Inbox and integration exceptions, merged into one dated, owned queue)
3. **What's expiring** (BAAs, credentials, certificates, licenses, training, drills)
4. **The binder** (last quarter's evidence, one click to print, with its exception count on the tile)

Everything else in this cluster should be a detail view reachable from one of those four, and **no item in this cluster should own a top-level navigation entry named after a compliance concept.** Language throughout should be the practice's: "insurance check" not 270/271, "message that couldn't be delivered" not dead letter, "who can see what" not RBAC, "try sending again" not replay. The strongest signal of success would be an office manager who never uses the words HIPAA, audit, or integration while operating the system correctly all year.

**The deepest intuitiveness lever is not UI — it is removing the decision.** Every security dial not shipped is a question she cannot get wrong, a support call that never happens, and a drift vector that cannot open. The most intuitive trust plane is the one with no settings page.

## Top 3 reframings

1. **"Portable HIPAA control plane" → "The practice never prepares for an audit, because the binder was already printed last Friday."** Compliance becomes a subscription to a recurring artifact produced by work that had to happen anyway, not a console the practice operates. Everything in the security doc should be judged by whether it puts a page in that binder without anyone doing anything unusual.

2. **"Minimum-necessary authority designer" → "Payroll is the access control system."** Access is a pure function of employment facts — who works here, in what job, at which locations, from when to when. There is no permissions screen; there is a staff roster. This single move collapses the RBAC matrix, the offboarding gap, the access-review campaign, the temp-worker problem, and (if automations are modeled as employees) the automation-scope model into one object the office manager already maintains for other reasons.

3. **"Interface operations console" → "Nothing is stuck — and if it is, we already told you, told you what it costs, and put it on someone's list."** Integration health is a status line plus a work queue plus a silence detector, not a dashboard. This reframe also fixes the cluster's most dangerous blind spot, because a console reports what went wrong while a practice actually loses money to what quietly never happened.

*(Runner-up, and the most structurally consequential: **"self-managed compliance" → "a hosted control plane does the trust work; the data plane stays portable."** Attestation collection, offsite immutable backup, drill scheduling, evidence generation, credential expiry, and anomaly alerting run centrally for practices that want it and on-bench for those that do not. This answers "no IT department" directly rather than mitigating it, and it satisfies constitution Principle VI so long as every function remains runnable locally.)*

## Biggest concern

**The cluster is engineered for the right threat model and the wrong operator, and the mismatch is concentrated entirely in the surfaces.** Nearly every underlying capability here is correct and several are genuinely differentiating — but they are packaged as enterprise instruments (a matrix editor, a console, review campaigns, a developer program, signed packs) for a practice whose only compliance operator is a person with eleven other jobs. The concrete failure mode is not that the beta ships insecure software; it is that the beta ships technically excellent trust machinery that the office manager never opens, so the evidence is generated and unused, the exceptions are raised and unworked, and the practice still fails its insurance questionnaire — after openPractice spent a large share of beta engineering on it.

Compounding this: the cluster's coverage is inverted against real risk. The items fully specified (RBAC matrix design, accounting of disclosures, public API versioning, automation scope negotiation) are low-yield at this practice size, while the events that actually generate OCR findings, downtime, and existential loss — same-day offboarding, patient right of access, missing BAAs, credential expiry, ransomware recovery, and upgrade safety — are absent or reduced to a single clause. **If exactly one thing changes from this review, it should be that the beta's trust scope is re-cut around the twelve missing feats and the four visible surfaces, with the matrix editor, the console, the disclosure accounting, and the public API program explicitly deferred.**
