# Feat Review: Revenue Integrity And Product Shape

Status: brainstorm
Kind: review
Summary: the revenue-integrity cluster models the payer contract as law and the close as a ceremony when a small practice needs payer promises remembered from its own remittances and a bank line that always has an owner — and the sequencing docs leave contract expectation, variance, credit control, and estimates in no release slice at all, so the packet's headline differentiator has no beta home.
Topics: openpractice-feat-review, revenue-integrity, product-shape
Captured: 2026-08-26

## How this review judges

Two people feel this product. The **owner-physician** looks at money weekly, in about five minutes, and asks three questions in this order: did the deposit come in, is anything stuck, and does it tie. The **bookkeeper** touches openPractice once or twice a month and wants a number that reconciles to a bank line without a conversation. Nobody in this building is a revenue-cycle analyst, nobody will maintain a contract-term precedence hierarchy, and nobody will perform a period close ceremony that QuickBooks already performs.

A feat earns "essential-core" if its absence produces a weekly moment of confusion about real money. It earns "differentiator" if it produces a moment incumbents cannot produce. Everything else is measured against the cost of the configuration it demands from a five-person office.

### Verdict summary

| # | Feat | Verdict |
|---|---|---|
| F1 | Versioned participation registry | essential-core |
| F2 | Governed reimbursement terms | questionable |
| F3 | Expected-allowable calculation | differentiator |
| F4 | Remittance variance detection | essential-core |
| F5 | Underpayment workbench | valuable-enhancer |
| F6 | Contract performance view | defer |
| F7 | Balanced healthcare subledger | essential-core |
| F8 | Deposit and settlement workbench | essential-core |
| F9 | EFT-to-ERA reassociation | essential-core |
| F10 | Unapplied and credit-balance control | essential-core |
| F11 | Controlled close and correction | questionable |
| F12 | Balanced accounting export | essential-core |

Distribution: essential-core 7, differentiator 1, valuable-enhancer 1, questionable 2, defer 1.

---

# Part A — Payer Contract And Reimbursement Integrity

## F1 — Versioned participation registry

**Verdict: essential-core.** "Is Dr. Nguyen live with Aetna yet?" is asked in a small practice roughly weekly, and getting it wrong produces a denial class (provider not eligible on date of service, wrong billing NPI, wrong payer-assigned provider number) that is expensive to unwind because it surfaces 45 days after the mistake. The owner-physician feels it as a batch of claims that all failed for the same silly reason. The registry's real weekly job, though, is not legal record-keeping — it is supplying the payer-assigned provider identifiers and the effective dates that the claim builder and the scrubber need.

**Make it more useful**

- Store what actually goes on the claim: payer-assigned provider ID, group/billing NPI, taxonomy, and the loop it belongs in (billing provider vs rendering provider), not just an abstract participation fact. A registry that cannot pre-fill and validate a claim is a filing cabinet.
- Make it a **pre-submission gate**, not a report. The scrubber should hold a claim whose service date falls outside the rendering provider's participation window for that payer product and say so in one sentence: "Aetna Commercial: Dr. Nguyen effective 2026-09-01; this DOS is 2026-08-14. Bill under the group, hold, or override."
- Add an **effective-date countdown** and a "claims waiting on this effective date" dollar total, so the practice sees the value of the pending enrollment and knows to hold rather than submit-and-deny.
- Model the three states that matter and no more: participating, not participating (out-of-network), and pending-with-expected-date. Retro-effective dates are common; support "effective back to 2026-06-01, approved 2026-09-10" and auto-list the claims now eligible for resubmission.

**Alternate/better solutions**

1. *Simpler:* collapse the registry into the provider-enrollment record entirely. One child table per provider: payer product, effective from, effective to, payer provider ID, status. No separate "Payer Agreement" object in beta. The agreement document itself becomes an attachment with a date range — evidence, not a modeled entity. This removes an entire DocType family and loses nothing the beta needs.
2. *Inverted:* derive participation from remittance reality. If a payer paid you under a contractual (CO-45) adjustment on a date, you were participating on that date. Show observed participation next to declared participation and flag disagreement. This catches the silent case the registry always misses — the practice believes it is in-network, the payer's file says otherwise, and the patient is getting out-of-network cost-sharing.
3. *Externalized:* poll the payer's own directory or the CAQH/PECOS record where available and treat a mismatch as work. Directory attestation is already in the provider-enrollment atomic; the same fetch answers "am I in their file" for free.

**Reframe**

Not "record which agreements cover which providers." Instead: **"never send a claim that a payer's own file will reject for who sent it."** Participation is a claim-time precondition with a date, not a contracts database.

**Gaps it leaves**

Out-of-network claims are unaddressed and they are where the money and the patient anger live — an out-of-network claim frequently pays the *patient* directly, so the practice has a service rendered, a check mailed to someone else, and a full-charge patient balance to collect. Nothing here models payer-pays-patient. Also unaddressed: the single-case agreement or letter of agreement negotiated for one patient, which is common in small specialty practices and has no home in the model.

---

## F2 — Governed reimbursement terms

**Verdict: questionable as scoped.** Fee schedules, percentages, case rates, carve-outs, modifiers, exclusions, precedence, source evidence, and approvals is a contract-management product, and no small practice will populate it. The design partner will have paper or PDF contracts, at least one payer whose fee schedule they have never actually seen, and zero appetite for an approval workflow among three people. The 15% of this that is essential — a table of allowed amounts by payer product, code, and modifier with an effective date — should be built; the governance apparatus around it should not.

**Make it more useful**

- Ship the **Medicare Physician Fee Schedule as data**. It is public (RVU file, GPCI by locality, conversion factor). Most commercial small-practice contracts are expressible as "X% of the current Medicare fee schedule for our locality." That turns a payer contract into *one number* the practice can actually enter, and it correctly updates every January without anyone touching it.
- When a real fee schedule exists, accept it as a spreadsheet paste with column mapping and a preview of "here are 12 codes you actually bill and what this schedule says" — the practice validates against its own top codes, not against 8,000 rows.
- Encode the four adjustments that break naive expectations, or explicitly declare the expectation invalid when they apply: multiple-procedure reduction, bilateral, assistant-at-surgery, and site-of-service differential. Silence on these makes every surgical or multi-line claim a false variance.
- Replace "approvals" with **provenance**: who pasted it, from what file, on what date, hash of the source. That is all the governance a five-person practice needs and it is strictly more useful in a dispute.

**Alternate/better solutions**

1. *Simpler:* one field per payer product — `pct_of_medicare` — plus an exception table for the handful of codes negotiated separately. Ninety percent of the value, one screen, ten minutes of setup. Case rates and carve-outs become exception rows.
2. *Learned:* skip loading entirely. Build the allowed-amount table from your own posted remittances (see F3 and the **Learned Allowable Baseline** missing feat). A practice that has been operating for a year has already been told the fee schedule, one remit line at a time, by the payer. The contract document is then only needed to *arbitrate* a dispute, not to run the system.
3. *Deferred-precision:* store terms as free text with an attached PDF and a "confidence: unmodeled" flag, and let the expectation engine simply refuse to produce a number for that payer. An honest "we don't know what Cigna owes for this" is more useful than a fabricated expectation, and it costs a day of work instead of a quarter.

**Reframe**

Not "represent the contract." Instead: **"know the one number that turns a charge into an expected allowed amount, and be honest when you don't know it."** The contract is evidence for a human argument, not an executable artifact.

**Gaps it leaves**

Sequestration (the Medicare 2% reduction that arrives as CO-253) will read as a systematic 2% underpayment on every Medicare line unless it is modeled as a known deduction. Payer-side annual fee-schedule updates that arrive without notice are invisible to a loaded schedule — the loaded number silently becomes wrong and every claim reads as an underpayment or an overpayment. Cash and self-pay patients have no contract, so this feat produces nothing for them; the practice's own discount policy is a "reimbursement term" too and has no home.

---

## F3 — Expected-allowable calculation

**Verdict: differentiator.** This is the single capability in the packet that OpenEMR does not have and that Tebra/Kareo does not do well, and it is the engine behind both the variance work and a truthful patient estimate. It earns differentiator rather than essential-core only because a first beta can survive without it, and because the *contract-derived* version described here is the expensive way to get it.

**Make it more useful**

- Return a **band with a confidence and a sample size**, never a bare number: "expected $84–$91, based on 23 Aetna Commercial payments for 99213 in the last 12 months." A range with evidence is trusted; a point estimate that misses is never trusted again.
- Compute it at two moments and store both: at **scheduling/estimate time** (drives what the patient is told and what is collected at the desk) and at **claim submission time** (drives variance). Estimate-time expectation is the higher-value moment and the packet only implies the claim-time one.
- Emit an explicit `unsupported` result with a reason code — no schedule, provider not participating on DOS, modifier combination not modeled, code not in schedule — and let downstream consumers branch on it rather than on a null.
- Make the trace human-legible in one line, not a rule dump: "Medicare 2026 locality 01 for 99213 = $91.15 × 105% Aetna = $95.71, less 2% sequestration if Medicare = n/a."

**Alternate/better solutions**

1. *Simpler and probably better:* **expectation as a statistic, not a derivation.** Median allowed amount by (payer product × CPT × primary modifier × place of service) over trailing 12 months, with n and interquartile range. It requires zero contract entry, gets more accurate every week, automatically absorbs sequestration, multiple-procedure reductions, and mid-year fee-schedule changes, and it degrades gracefully — low n simply means a wide band. Contracts, when present, override the statistic and disagreement between them is itself the most valuable alert in the product.
2. *Even simpler:* "last time" lookup — the most recent allowed amount for this payer and code, with its date. One query, no model, and it answers the practice's actual question ("is this check smaller than usual?") for the top 20 codes that are 80% of volume.
3. *Charge-side inversion:* rather than predicting the allowed amount, predict the **contractual adjustment percentage** per payer. Practices reason in "Aetna pays about 62% of billed" far more naturally than in fee schedules, and it makes a wrong charge master visible immediately.

**Reframe**

Not "calculate what the contract entitles us to." Instead: **"remember what this payer has actually paid us for this code, and notice when that changes."** Expectation is memory plus a tolerance, not law plus arithmetic.

**Gaps it leaves**

Secondary and coordination-of-benefits claims have no defined expectation — after a primary's OA-23, which contract governs, and is the secondary's obligation the balance, the lesser-of, or nothing? Undefined here and undefined in the claims atomic. Capitated lines (which arrive with a $0 payment and a CO-24 adjustment) would read as total underpayments. Bundled/global-period lines (CO-97) are legitimately $0 and must not create variance. Anything that never becomes a claim — cash patients, no-show fees, records fees, cosmetic or non-covered services — gets no expectation at all, and no other feat in this cluster picks them up.

---

## F4 — Remittance variance detection

**Verdict: essential-core.** This is the moment the product justifies its existence: the payer paid, the practice has no idea whether the amount was right, and today the answer is "the check cleared, so I guess it's fine." It is felt weekly in aggregate even though no individual line is felt. The important design consequence is that a small practice cannot work variances one at a time — the unit of attention must be a pattern, not a line.

**Make it more useful**

- Separate **line variance** from **remit variance**. Line variance is "we expected $91, we got $84." Remit variance is "the sum of the claim payments on this remit is $4,120 but the check is $3,940." The second is the one the owner-physician can see with their own eyes, and only the PLB segment explains it (see missing feats).
- Suppress the known-benign classes explicitly and by name, not by tolerance: CO-97 bundling, CO-24 capitation, CO-253 sequestration, OA-23 prior-payer adjustment, and multiple-procedure reductions. A tolerance band alone will either drown the practice or hide a real 3% haircut.
- Aggregate before assigning. The default output should be a weekly digest of the form "Cigna paid 8% less than baseline on 99214 across 22 claims, $310" — one row, one decision — rather than 22 cases.
- Detect **overpayments** with the same machinery and the same seriousness. A payer that pays too much is a liability with a 60-day clock on the Medicare side and a takeback in your future on the commercial side, and the packet's language is entirely about underpayment.

**Alternate/better solutions**

1. *Simpler:* **drift detection with no contract at all.** Weekly, compare each payer × code's trailing-4-week median allowed amount against its trailing-52-week median. Alert on shifts beyond tolerance. This catches a payer's silent January fee-schedule cut, a plan migration, and a coding change, needs no contract data, and produces perhaps three alerts a week instead of two hundred cases.
2. *Peer-free benchmark:* compare each payer against *your other payers* for the same code. "Everyone pays $88–$96 for 99213; UHC pays $71" is a negotiation fact the practice can act on annually and requires nothing but its own history.
3. *Charge-master safety valve:* run the inverse check — flag codes where the payer's allowed amount **equals** your billed charge, which almost always means you are billing below the contracted rate and leaving money on the table. It is the cheapest variance check in existence and no incumbent surfaces it to a small practice.

**Reframe**

Not "detect variance on every remittance line." Instead: **"tell the practice, once a week, the two or three ways payers changed their behavior."** The output is a short list of payer behaviors, not a queue of claim lines.

**Gaps it leaves**

Interest payments (Medicare's PLB `L6` on clean claims paid late) arrive as unexplained extra money and will read as an overpayment; they are also *other income* for tax purposes, not patient-service revenue. Takebacks against a *prior* claim (PLB `WO`) reduce the current check and, if netted naively, present as a variance on an unrelated claim. Forwarding balances (`FB`) carry money across remits and make each remit individually unbalanced by design. None of these appear anywhere in this document.

---

## F5 — Underpayment workbench

**Verdict: valuable-enhancer.** The economics are brutal at small-practice scale: a typical underpayment is $10–$40, a reconsideration takes 20–40 minutes with a phone hold, and the overturn rate on contractual underpayment (as opposed to denial) is low. The practice will not staff this. It is worth building only in a form where the *aggregate* pattern is what gets escalated, and where the case machinery is inherited rather than newly built.

**Make it more useful**

- Do not build a second workbench. Underpayment is a **disposition on the existing denial/claim work queue** described in the claims atomic. One place billers work claim problems, with a reason facet.
- Attach a **recovery-vs-effort estimate** to every proposed case and default to closing anything under a configurable threshold as "accepted variance — recorded, not pursued." Recorded-and-not-pursued is still evidence; it aggregates into the annual negotiation fact.
- Make the primary action **batched correspondence**: one letter or portal message per payer per week listing all affected claims, rather than one action per claim. This is how underpayment recovery is actually done profitably.
- Track the two deadlines that differ from denial deadlines — the contractual reconsideration window (often 90–180 days from remit, set by contract not by payer policy) and the timely-filing window for a corrected claim — and show which one is closing.

**Alternate/better solutions**

1. *Simpler:* no workbench at all in beta. A single report — "underpayments by payer, this quarter, with claim list export" — that the practice takes to its annual contract conversation. Recovery becomes negotiation leverage rather than per-claim labor, which is where the actual return is for a practice this size.
2. *Outbound-only:* generate the appeal packet (claim, remit line, contract excerpt, expected calculation) as a PDF and stop there. Do not model the case lifecycle; model the artifact. Most small practices track the follow-up in the payer portal anyway.
3. *Systemic-cause routing:* classify each variance to a *cause* — contract data wrong, our coding wrong, payer wrong — and route the first two to configuration fixes instead of recovery. Most "underpayments" in a first year are the practice's own bad data, and fixing the cause is worth 50× the individual recovery.

**Reframe**

Not "recover underpaid claims." Instead: **"convert payer behavior into either a configuration fix, a batch letter, or a negotiation fact — and admit which lines aren't worth touching."**

**Gaps it leaves**

The mirror workflow is missing entirely: the **payer overpayment demand letter**. A letter arrives, gives 30 days, offers refund-or-offset, and if ignored becomes a recoupment against future checks. That is a real, dated, evidence-bearing case file with a money consequence and no home in this feat or any other. Also missing: the appeal that *succeeds* — where does the additional payment land, how does it reconcile to a deposit, and what reopens on the patient's balance if their responsibility drops.

---

## F6 — Contract performance view

**Verdict: defer.** Realization, denial, underpayment, turnaround, administrative cost, and renewal observations require a year of clean history to mean anything, and a first beta will have three months of partly-wrong data. Shipping it early produces charts the owner-physician will correctly distrust, which poisons the credibility of every other number in the product. It is also substantially duplicated by "payer accountability scorecards" already claimed by the claims-and-remittance atomic — a seam that should be resolved before either is built.

**Make it more useful**

- When it does ship, anchor it to a decision with a date: contract renewal. A payer view that appears 90 days before the renewal/termination date, pre-populated with the year's evidence, is used; a permanent dashboard is not.
- Include the cost side honestly: days-to-payment, denial touches per claim, and portal-time. Small practices routinely stay in a network that costs more to bill than it pays, and no incumbent tells them.
- Include **payer mix concentration** and the counterfactual: "Aetna is 31% of your collections; at their current rates, dropping them costs $X unless you can refill 60% of those slots."

**Alternate/better solutions**

1. *Simpler:* one annual PDF per payer, generated on demand, six lines long: collections, average allowed vs your other payers, denial rate, days to pay, underpayment total, renewal date. Print it, take it to the meeting.
2. *Comparative rather than absolute:* skip realization math entirely and rank payers against each other on your own book of business. Relative facts survive dirty data far better than absolute percentages.
3. *Event-driven:* no view at all — just an alert when a payer's aggregate behavior crosses a threshold ("UHC's average days-to-pay went from 18 to 34 this quarter"). Three alerts a year beats a dashboard nobody opens.

**Reframe**

Not "expose contract performance." Instead: **"once a year, per payer, hand the owner one page that makes the renewal conversation different."**

**Gaps it leaves**

Administrative cost is asserted but unmeasured — nothing in the packet captures staff touches per claim, so "administrative cost" has no source. The view is also payer-only; the practice's largest financial relationships may be its merchant processor (2–3% of every patient dollar) and a patient-financing partner, and neither gets performance scrutiny anywhere in the packet.

---

# Part A — Financial Control And Accounting Bridge

## F7 — Balanced healthcare subledger

**Verdict: essential-core.** Everything else in this cluster is a projection of this. Without an immutable, balanced event ledger there is no provenance claim, no trustworthy export, and no defensible answer to "why does the report say something different than last month." The risk is not whether to build it but how much accounting theology to import while building it.

**Make it more useful**

- Use Frappe's native immutability rather than inventing it: submittable DocTypes with `docstatus` 0/1/2, and cancel-and-amend for corrections. This gives reversal chains, amendment lineage, and permission gating for free, and it is exactly the pattern a Frappe-literate maintainer will expect.
- Define the **reason-code taxonomy first**, because it is the join key to the general ledger, and get it right: contractual adjustment, sequestration, courtesy/prompt-pay discount, small-balance write-off, bad debt, charity, refund, takeback, interest income, merchant fee, chargeback, chargeback fee, NSF/returned item, financing discount. Every one of those maps to a different account and a different tax treatment; a generic "adjustment" type destroys the export.
- Carry both **service date and posting date** on every entry and make every report state which it uses. Almost every "the numbers changed" complaint in a practice is a service-date vs posting-date confusion.
- Store the raw source with a hash as an attachment on the entry batch — the 835 file, the bank CSV, the processor settlement report. Provenance that cannot produce the original document is a claim, not evidence.

**Alternate/better solutions**

1. *Simpler:* a **cash-basis-first ledger**. Most small ambulatory practices file taxes on cash basis, so charges and accounts receivable never enter their books at all. Build the receipts/allocations/refunds side as the balanced core, and treat charges and A/R as a *memo* accrual view for management reporting only. This halves the entity count and matches how the bookkeeper actually thinks.
2. *Borrowed:* model entries in a shape that is trivially convertible to both an ERPNext Journal Entry and a QuickBooks JournalEntry object — same field names where possible — without taking a dependency on ERPNext's accounts module. Portability of the export shape is worth more than reuse of the engine.
3. *Event-sourced:* store only immutable domain events (charge posted, remit line posted, receipt received, allocation made) and derive the balanced journal as a pure function at export time. Corrections become new events; there is no posted state to reverse. Harder to explain, dramatically easier to keep correct.

**Reframe**

Not "maintain a balanced subledger." Instead: **"every dollar that touches this practice has one immutable row, one reason, and one destination account — and the row is never edited."**

**Gaps it leaves**

Money that arrives without a patient has no natural home: payer interest, a settlement check, a records-request fee, a vaccine rebate, a returned patient refund check. If the only entry point is a patient obligation, all of these become "unapplied cash" forever. There is also no non-patient *outflow* concept, which is where refunds to payers and merchant fees actually live.

---

## F8 — Deposit and settlement workbench

**Verdict: essential-core, and the highest-value feat in the cluster for the owner-physician.** "The bank shows $6,412 and I don't know why" is the single most common weekly money confusion in a small practice, and no incumbent at this price point answers it. This is where the product's differentiation is actually *felt*, as opposed to F3, where it is intellectually superior.

**Make it more useful**

- Invert the workbench: make the **bank line the unit of work**, not the batch. Import the bank statement (CSV or OFX; a monthly manual upload is fine for beta) and require every credit line to be explained by exactly one settlement object. The screen is a to-zero list, and the daily question becomes "is anything on the bank statement unexplained."
- Handle both processor funding models explicitly: **gross funding** (deposits equal receipts, fees debited monthly as a separate line) and **net funding** (deposits equal receipts minus per-batch fees). Receipts posted at gross will never tie to a net deposit, and the difference is not an error — it is an expense that must be booked as one.
- Model the **timing offsets** as first-class, not as exceptions: card batches settle T+1 or T+2, ACH refunds clear on their own schedule, and a Friday batch lands Monday. An unexplained line that is simply not yet funded should say "expected Monday," not "unmatched."
- Show a **daily expected-deposit number** before the money arrives. Practices reconcile far more willingly when the product predicted the deposit rather than merely audited it afterward.

**Alternate/better solutions**

1. *Simpler:* a "**bank line inbox**." Upload the statement, and each unexplained credit or debit is one card with three buttons: match to a settlement we know about, classify as a known non-patient item (interest, transfer, rebate), or open a question. No batch modeling, no expected/observed duality — just an inbox that must reach zero.
2. *Processor-first:* skip the bank in beta and reconcile only to the processor's and payer's own settlement reports, then let QuickBooks' bank feed do the bank match. Weaker, but it eliminates statement-import work entirely and QBO's bank feed is genuinely good at this.
3. *Deposit-slip generation:* for cash and checks, have the product *produce* the deposit slip from the day's receipts. If openPractice defines the deposit, the deposit always matches by construction, and the reconciliation problem for cash simply disappears.

**Reframe**

Not "group receipts into settlement events." Instead: **"every line on the bank statement has an owner and a one-sentence explanation, or it is red."** Reconciliation is a to-zero worklist keyed to bank lines, not a report.

**Gaps it leaves**

Bank-deposit variance sources beyond fees are unmodeled: NSF/returned patient checks that reverse a deposit days later, a bank's own returned-item fee, a processor reserve hold on a large transaction, and a deposit that a payer splits across two ACH transfers. Merchant fee treatment is named once ("fees") and never given an accounting destination. The cash drawer itself — opening float, over/short, who counted it — has no representation, and cash over/short is the small practice's most common integrity failure.

---

## F9 — EFT-to-ERA reassociation

**Verdict: essential-core.** Every practice lives this: the money is in the bank Tuesday, the ERA shows up Thursday from a different system, and nobody is certain they are the same event. It is felt weekly, it directly blocks posting, and it is a genuinely hard matching problem that a small practice solves today by squinting at amounts.

**Make it more useful**

- Match on the actual reassociation key, not on amount heuristics: the ERA's `TRN02` reassociation trace number against the ACH addenda record, with payer ID (`TRN03`) as a secondary. Amount+date matching should be the *fallback*, and matches made that way should be labeled as inferred.
- Build **two symmetric orphan queues** — orphan ERA (remit with no money) and orphan deposit (money with no remit) — and let them meet. This framing makes the ambiguity visible instead of hiding it inside a match engine, and "money arrived with no explanation" and "explanation arrived with no money" are genuinely different problems with different owners.
- Detect the **duplicate ERA** and the **reversal/corrected remit** as named cases. A payer reissuing a corrected 835 with negative CAS amounts against already-posted lines is the single most dangerous auto-posting scenario, and it must never auto-post.
- Surface an **enrollment gap** as work: if a payer is paying by paper check or the practice is not enrolled for ERA delivery, the reassociation will never work and the product should say so and produce the enrollment task, rather than accumulating orphans forever.

**Alternate/better solutions**

1. *Simpler:* do not automate the match at all in beta. Present a side-by-side "unmatched money / unmatched remits" screen sorted by amount, and let a human click two rows together in two seconds. For a practice receiving 10–30 remits a week, this is faster than tuning thresholds and has zero false-match risk.
2. *Payer-portal-free:* accept the clearinghouse's own reassociation where it already provides it, and treat openPractice's job as verification rather than matching. Most clearinghouses hand back the pairing; re-deriving it is work the practice already paid for.
3. *Amount-decomposition:* when a deposit does not match any single remit, attempt the *subset-sum* over open remits within the window and present the candidate combination. Payers routinely fund several remits in one transfer, and this one trick removes a large fraction of manual matching.

**Reframe**

Not "reassociate EFT and ERA." Instead: **"money and its explanation arrive separately; hold both until they meet, and never post an explanation as if it were money."**

**Gaps it leaves**

The check that arrives in the mail with a paper EOB — still a meaningful share of small-practice payer volume — has no path except "governed manual remittance," which is named in the sequencing doc but not modeled here. Virtual credit card payments from payers (which carry a 2–3% processing haircut and are effectively a payer-imposed fee) are entirely absent and are a growing, genuinely infuriating small-practice problem. Payer takebacks that reduce a deposit below the ERA's claim-payment total will make every affected deposit fail to reassociate until PLB is modeled.

---

## F10 — Unapplied and credit-balance control

**Verdict: essential-core.** Credit balances are not an aging bucket; they are a liability with a legal clock. Medicare overpayments carry a 60-day identify-and-report obligation, patient credits become unclaimed property under state law, and a practice with a growing unapplied-cash pile has a reconciliation that is quietly lying. The owner-physician feels this as "why does the report say we collected more than we billed."

**Make it more useful**

- Split the single bucket into the three things it actually contains, because they resolve differently: **unidentified receipts** (we have money, we don't know whose), **patient credits** (we know whose, we owe it back or forward), and **payer overpayments** (we owe it back, on a clock, or it will be taken back).
- Start a **named clock per bucket** and show days remaining, not days elapsed. "Medicare overpayment: 41 days left to report" is actionable; "aged 19 days" is not.
- Offer the three resolutions explicitly with different approvals: refund, transfer to another obligation of the same guarantor, and apply-to-future. Transfer across guarantors or legal entities should be prohibited outright, not merely discouraged.
- Add a **"why do we have this" classifier** — patient paid an estimate that exceeded actual responsibility, payer paid twice, primary and secondary both paid, patient paid after insurance already paid. The cause determines the resolution and is knowable from the ledger.

**Alternate/better solutions**

1. *Simpler:* one screen called "**money we owe back**," three sections, each row with a name, an amount, a days-left number, and a single button. No aging report, no ownership assignment, no workflow states. For a practice with a dozen of these at a time, workflow is overhead.
2. *Preventive:* attack the cause instead of the symptom — cap point-of-service collection at the low end of the estimate band rather than the midpoint. Under-collecting slightly produces a follow-up statement; over-collecting produces a refund, a phone call, a check, and a reconciliation item. The asymmetry is large and nobody designs for it.
3. *Auto-forward with consent:* capture, at the time of payment, a standing patient election to apply any credit to future visits within N months. This legally and practically eliminates most patient credit balances at a family practice and costs one checkbox on the receipt.

**Reframe**

Not "make credit balances visible with aging and ownership." Instead: **"the practice is holding somebody else's money; show whose, how long, and what happens if we keep it."**

**Gaps it leaves**

The refund *disbursement* half is missing — approval is modeled, but issuing the check or ACH, tracking whether it cleared, handling the returned or stale-dated refund check, and the escheatment endpoint are not. Refunds to a payer are procedurally different from refunds to a patient (form, claim reference, and the choice between sending a check and electing offset) and are treated identically here. And a refund issued for a payment made by card should usually be a card *refund* to the original method, not a check — which is a merchant-processor operation with its own fee behavior.

---

## F11 — Controlled close and correction

**Verdict: questionable.** Period close is an accounting ceremony imported into a product whose users do not perform accounting ceremonies. The design partner's actual close happens in QuickBooks, performed by a bookkeeper, once a month or once a quarter. An openPractice period lock will be experienced as an obstacle the first time a late ERA arrives, and staff will either work around it or call for help. The *immutability* underneath is essential; the *close* on top is not.

**Make it more useful**

- If it ships, make the freeze attach to **what was published**, not to a calendar: an export batch and a report snapshot are immutable; the period itself stays open forever. This gives reproducibility (the actual goal) without blocking anyone.
- Support **as-of reporting** as the primary mechanism: any report can be run "as the books stood on 2026-07-31," derived from posting dates on immutable rows. This delivers everything a close delivers, with no ritual and no lock.
- If a lock is truly needed, make it **soft with a reason**: posting into a closed period is allowed, records the actor and reason, and automatically produces a delta export batch for the receiving system. Never make a correct late posting impossible.
- Never lock the patient's balance. Whatever the accounting period does, a patient's obligation must remain correctable, because a chargeback or a corrected remit in September legitimately changes a July balance.

**Alternate/better solutions**

1. *Simpler:* **no close at all.** Entries are immutable at post time and carry a posting date; exports are versioned batches; reports are as-of queries. Month-end becomes "print the report." This is strictly less code and strictly more usable.
2. *Bookkeeper-led:* let the *accounting system* own the close, and have openPractice honor it — read the QuickBooks closing date, and after that date, route new activity into a "prior period, needs a catch-up entry" list that the bookkeeper picks up next month. The lock lives where the authority lives.
3. *Daily instead of monthly:* replace the monthly close with a **daily balanced day** — cash counted, card batch settled, receipts posted, day marked balanced. Small practices can actually do this, it catches errors within 24 hours instead of 30 days, and the month then closes itself.

**Reframe**

Not "freeze the period's financial interpretation." Instead: **"the books are always closed; month-end is just printing."** Immutability at post time plus as-of queries makes every historical view reproducible without a single lock.

**Gaps it leaves**

The scenarios that make close painful are exactly the ones unaddressed: a chargeback landing 60 days after a closed period, a payer takeback against a prior year, a refund check that never cleared, and a corrected 835 reversing posted lines. Each of these needs a defined answer to "which of {subledger reversal, export delta, patient statement recall, variance reopen} fires and in what order," and no document in the packet answers it.

---

## F12 — Balanced accounting export

**Verdict: essential-core.** This is the bookkeeper's entire experience of the product, and it is the difference between openPractice being the system of record and being a thing the bookkeeper re-keys from. But "versioned journal mappings, export batches, acknowledgment, rejection, and reconciliation status" describes an ERP integration program; the design partner needs a file that QuickBooks eats.

**Make it more useful**

- Ship a **QuickBooks-shaped default** with no configuration: one journal entry per bank deposit, dated on the deposit date, split by income account, with merchant fees as an expense line and refunds as their own line. Because the JE total equals the bank line, QBO's bank feed matches it in one click. That single design choice is the difference between an export that gets used and one that gets abandoned.
- Default to **cash basis** with accrual as an opt-in. Most small practices file cash basis; exporting charges and A/R to their books is not merely unnecessary, it actively breaks their tax reporting.
- Make the account mapping a screen with **at most eight rows**: patient service revenue, patient refunds, merchant fees, interest income, bank account(s), write-offs, undeposited funds, patient credits/liability. Present them with plain-language names and let the bookkeeper map each to their existing QuickBooks account from a dropdown.
- Provide a **one-page reconciliation certificate** with every export: "For July: collections $X, refunds $Y, fees $Z, net to bank $W; the bank shows $W; difference $0." This is the artifact the bookkeeper wants and it makes the export self-proving.

**Alternate/better solutions**

1. *Simplest and probably the right default:* a **deposit summary CSV**, not a journal at all. Date, amount, split, memo, bank account. It imports into QuickBooks, Xero, and Wave; it is readable by a human; and it needs no chart of accounts inside openPractice. Offer the full journal only when the practice runs accrual.
2. *API-direct:* write JournalEntry objects straight into QuickBooks Online via its API with an idempotency key, so there is no file handling and no re-keying. Acknowledgment and rejection then come from the API for free, which is exactly the "acknowledgment/reconciliation status" the doc asks for — obtained rather than built.
3. *Read-back verification:* rather than modeling acknowledgment state machines, periodically read the destination's totals for the period and compare to openPractice's. A one-line difference check is a better proof of successful export than a state machine that says "acknowledged."

**Reframe**

Not "publish balanced journal batches with acknowledgment lifecycle." Instead: **"the bookkeeper's bank feed matches the practice's deposit, one click per deposit, with nothing to re-key."** The export is a bank-reconciliation aid that happens to be a journal.

**Gaps it leaves**

Three revenue classes have no mapping story: interest income from payers (other income, not patient revenue), patient-financing settlements (revenue at full balance, with the financing discount as an expense), and non-patient receipts. Chargeback fees and chargeback reversals need their own accounts and none are named. Sales-tax-bearing items — retail products, supplements, cosmetic services — exist at many small practices and would need a tax treatment the packet never mentions. And there is no export for the *other* direction: the practice's own payments to a collection agency or a billing service as a percentage of collections.

---

# Part B — The framing decisions

## The five most consequential decisions

### D1 — The companion app is deferred; responsive web plus assisted channels are the beta baseline

**Agree, strongly, with one carve-out.** A native app would add account federation across two backends, identity binding, app-store release cycles, push infrastructure, and a third candidate source of truth — and at one small practice, adoption would land somewhere under 20% of patients in the first year. Nothing in the money loop needs it. The `launch-archetype` doc's additional observation that the HealthLinc name is already a registered mark of an existing community health center is a second, independent reason to defer, and the packet is right to treat the name as unresolved.

The carve-out: deferring the app must not defer **the patient's own money view**. A mobile-web page showing "what you owe, why, and pay now" from a texted link is load-bearing for collections and for the product's "estimate-to-payment lineage" thesis, and right now it is buried inside "responsive previsit tasks" in Slice 1 with no money content. Name it as its own beta-scoped surface, or patient collections in beta will be paper statements plus a phone call — which is the exact OpenEMR experience the packet promises to beat.

### D2 — The beta is one complete thin slice from setup through accounting export, not an access-only or claims-only prototype

**Agree.** This is the bravest and best call in the packet. The product's differentiation claim is *provenance across the whole money path*; a half-loop cannot test it, and the seams between domains are exactly where the design will be wrong. The `scope-and-sequencing` doc's own tension section states this correctly.

The pressure this creates should be relieved in a specific place. When Slice 2 runs long — and it will — cut the **contract-expectation engine**, not the reconciliation. Reconciliation to a bank line is the part that cannot be simulated later, that produces the weekly felt value, and whose absence makes every other number unverifiable. Expectation can arrive in month four as a statistic over the history the beta has by then accumulated (see D3), and it will be *better* for having waited.

### D3 — Reimbursement expectation is modeled as contract-derived (agreement → schedule → override → expected result), with "learned from history" relegated to a signal

**Challenge — invert it.** The payer-contract doc says historical patterns "must remain signals rather than disguised contractual truth." That is the right legal statement and the wrong product default. For the actual beta customer, the contract-derived path requires data they do not have in a form they cannot produce, while the learned path requires nothing and gets better weekly. It also absorbs, for free, every effect the modeled path must be taught individually: sequestration, multiple-procedure reduction, mid-year fee-schedule changes, plan migrations, site-of-service differentials.

The honest framing preserves the legal caution without paying for it upfront: **the learned baseline is the operational expectation; the contract is the arbiter**. When both exist and disagree, that disagreement is the single most valuable alert in the product — it means either the contract was loaded wrong or the payer is not honoring it, and a small practice cannot currently detect either. Default the beta to learned-only, make contract loading optional and per-payer, and let the practice load one contract for the payer they most distrust.

### D4 — Period close is a first-class controlled ceremony with freezes and compensating entries

**Challenge.** This is enterprise accounting muscle memory applied to a customer who does not have an accounting department. The real close happens in QuickBooks; an openPractice close is a second ceremony with no owner. The first late ERA or 60-day-old chargeback will meet the lock, and the practice will either be blocked or will learn to reopen periods routinely, at which point the control is theatre.

Replace it with immutability at post time, posting-date-stamped rows, as-of queries, and versioned export batches as the only freeze. That yields reproducible reporting — the genuine requirement behind the close — with no lock and no ritual, and it fits the "manual-safe, understandable exception" values the archetype doc otherwise upholds. If the packet wants a ritual, make it the **daily balanced day**, which a small practice can actually perform and which catches errors 30 days sooner.

### D5 — Estimates and the Good Faith Estimate are handled as an editorial "current corrections and refreshes" note rather than a beta-scoped money path

**Challenge — this is the packet's weakest treatment of a real obligation.** The `feature-normalization` doc records the GFE correctly as a bullet: distinguish insured estimates from the written self-pay GFE and its dispute path. But then nothing carries it forward. Search the four slices in `scope-and-sequencing`: there is no estimate, no GFE, no time-of-service collection, and no self-pay path anywhere. Slice 1 is "financial preparation" that prepares no finances — it does eligibility and authorization and stops. Slice 2 calculates patient responsibility only *after* adjudication.

Three consequences, all bad for a small practice. First, the beta collects no money before or at the visit, which is the highest-yield cash lever a small practice has and the one thing that most changes their week. Second, the GFE is a legal obligation for uninsured and self-pay patients with a dispute threshold at $400 over the estimate; a product that touches self-pay billing and does not issue or retain a GFE is exporting risk to the customer. Third, and structurally worst: **cash and self-pay patients do not exist anywhere in the revenue-integrity cluster.** Every path in it begins with a claim. A practice with 8% self-pay has 8% of its revenue outside the product's entire integrity model, including its discount policy, its receipts, and its GL destination.

Promote estimates and self-pay to Slice 1/2 with a named money path. This is the highest-value change available to the sequencing plan.

## Runner-up decisions, briefly

- **One connector per class, with certified test traffic possibly sufficient for the first milestone** (`scope-and-sequencing`, open question). Agree on one-per-class; **challenge** the test-traffic escape. A beta that never receives a real ERA and a real merchant settlement cannot test reassociation, PLB, fees, or chargebacks — precisely the mechanisms this cluster exists to get right, and precisely the ones that only misbehave with real data. Require at least one live payer ERA and one live merchant settlement, even at low volume.
- **The eleven-point feature contract** (`feature-normalization`). Agree. It is heavy, but items 4 (state machine including correction and reopening) and 8 (exception and assisted fallback) are the two that will actually save this project, and both are habitually skipped without a checklist.
- **Risk-based deferral of AI, prediction, and sensing** (`scope-and-sequencing`). Agree without reservation. The prerequisite argument in `synthesis-product-shape` — that denial prediction cannot be meaningful before denial disposition history exists — is correct and well made.
- **Two-level catalog with capability classes** (`feature-normalization`). Agree; the reclassification of HealthLinc, mobile previsit, AI intake, and arrival sensing from "domains" to "channels and enhancements" is the single clarifying move that makes the rest of the packet coherent.
- **Nothing in the packet should be cut for being over-included**, with two exceptions: F6 (contract performance view) is premature, and F11's close ceremony should be replaced rather than built. Everything else in the two capability docs is either core or a defensible enhancer.

## The structural finding

The `scope-and-sequencing` slices and the `synthesis-revenue-integrity` cluster **do not agree with each other**. Trace the cluster's headline capabilities into the slices:

| Capability | Slice |
|---|---|
| Charge, 837P, 835 posting, statement, patient payment | Slice 2 ✓ |
| Deposit/settlement matching, subledger balance, journal export | Slice 2 ✓ |
| Unapplied cash | Slice 3 (as a work-routing item only) |
| Participation registry / payer allowed-amount schedules | **no slice** |
| Expected-allowable calculation | **no slice** |
| Remittance variance detection | **no slice** |
| Underpayment work | **no slice** |
| Credit balances and refund control | **no slice** (only "basic refund proposal") |
| Patient estimates and GFE | **no slice** |
| Contract performance | **no slice** |

Slice 0 configures "core policies" and a payer/clearinghouse route but never establishes a payer product dictionary or an allowed-amount schedule, while Slice 2 assumes "versioned codes and fees" for charging. So the beta as sequenced posts remittances and exports a journal without ever forming an expectation — which is precisely the OpenEMR behavior the packet sets out to beat. Either the slices should absorb a reduced form of contract expectation (the learned baseline of D3 is cheap enough to fit), or the synthesis should stop presenting expected-vs-settled as a beta capability. The current state lets the differentiator become post-beta silently.

---

# Cluster level

## Missing feats

- **Payer-Level Adjustment (PLB) Ledger** — first-class handling of the 835 PLB segment so that takebacks (`WO`), forwarding balances (`FB`), interest (`L6`), capitation, and early-payment allowances resolve to the original claim or to a named non-patient account. Without it, the check amount will never equal the sum of claim payments and every affected deposit fails reassociation for reasons nobody can see.
- **Recoupment And Overpayment Case File** — the full arc from demand letter through appeal-or-agree to either a refund disbursement or an offset election, ending at the specific future remittance line where the offset actually lands. This is the most common way a small practice loses track of real money and it appears nowhere in the packet.
- **Merchant Settlement, Fee, And Chargeback Reconciliation** — gross vs net funding, per-batch and monthly fees, reserve holds, chargebacks, chargeback fees, and returned items, each posting to an expense or liability account rather than being absorbed into contractual adjustments. Card processing is a 2–3% tax on every patient dollar and the packet mentions fees exactly twice without giving them a destination.
- **Self-Pay And Time-Of-Service Money Path** — a priced, discountable, receipt-bearing, GL-destined revenue path for patients who never generate a claim, including cash discount policy and the accounting distinction between a courtesy discount and a write-off. Every path in the current cluster begins with a claim.
- **Good Faith Estimate Register And Dispute Clock** — issue, version, deliver, and retain the written GFE for uninsured and self-pay patients, then watch billed-versus-estimated variance and assemble a dispute-ready evidence file when it crosses the threshold. Currently an editorial note with no owner.
- **Learned Allowable Baseline** — expected allowed amounts computed from the practice's own posted remittances (median by payer product × code × modifier × place of service, with sample size and spread), serving as the default expectation source and as the challenger to any loaded contract. Cheap, self-improving, and immune to every fee-schedule change the modeled path must be taught.
- **Payer Drift Alarm** — a weekly check comparing each payer's recent allowed amounts to its own trailing baseline, alerting on shifts beyond tolerance. This, not a case workbench, is how a small practice actually detects a payer quietly cutting rates.
- **Bank Line Inbox** — the bank statement as a to-zero worklist where every credit and debit must be explained by exactly one settlement, fee, transfer, or named non-patient item. Turns reconciliation from a report into a finishable task.
- **Refund Disbursement Register** — refunds tracked as a liability from approval through check or ACH issuance to clearance, with the Medicare 60-day clock, the stale-dated-check case, the card-refund-to-original-method rule, and an escheatment endpoint.
- **Patient Financing Settlement** — CareCredit/Cherry-style third-party financing recorded as a full-balance settlement net of a merchant discount, with recourse and refund behavior defined. Common in dermatology, dental-adjacent, and elective practices, and it silently breaks any ledger that assumes the payer and the patient are the only two sources of money.
- **Daily Balanced Day** — a five-minute end-of-day ritual reconciling cash drawer (with float and over/short), card batch, posted receipts, and deposit slip, producing a "day balanced" marker. Replaces the monthly close as the practice's actual control point.
- **Money-Day Predictor** — the expected deposit for tomorrow, derived from settled card batches, funded ERAs, and known payer cycles. Predicting the deposit before it lands is what earns the owner-physician's trust in every other number.

## Cross-feat gaps — the seams where money integrity breaks

1. **The remit-total seam.** The ERA's claim payments and the BPR check amount differ by PLB adjustments. The payer-contract doc never mentions PLB; the financial-control doc says "payer EFTs and fees." Nothing owns the difference, so it will surface as an unmatched deposit with no explanation available anywhere in the system.
2. **The variance-to-correction seam.** When a variance is classified "contract data defect," who fixes the schedule, does the expectation recompute retroactively, and do previously-closed variances reopen? Undefined. Without an answer, the first bad fee-schedule load poisons months of variance history permanently.
3. **The expectation-to-estimate seam.** Patient billing says estimates derive from "contracted rates"; the contract doc emits "contract-aware estimate inputs." Neither says what the patient is told when confidence is low. This is the seam where a wrong number becomes a refund, a phone call, and a lost patient.
4. **The reason-code-to-account seam.** Contractual adjustment, sequestration, courtesy discount, small-balance write-off, bad debt, refund, takeback, interest, merchant fee, chargeback, and financing discount all reach the journal export. The subledger promises "versioned journal mappings" but never defines the reason taxonomy that is the mapping's join key. Get this wrong and the export is unfixable downstream.
5. **The reversal-ordering seam.** A corrected 835 reversing posted lines in an exported period must trigger some ordered subset of {subledger reversal, export delta, patient statement recall, variance reopen, credit balance creation}. No document specifies the order or the trigger.
6. **The takeback-versus-deadline seam.** A recoupment against a two-year-old claim can arrive after the appeal window has closed. The timely-filing and appeal clocks live in claims; the takeback lives in financial control; nothing connects them, so the practice learns about the lost window after it has lost it.
7. **The participation-versus-service-date seam.** Enrollment owns effective dates, contracts own terms, claims owns submission — and nothing gates submission on the rendering provider being effective on the date of service. This is a pure pre-submission check that costs almost nothing and prevents a whole denial class.
8. **The COB seam.** After the primary's OA-23, which contract governs the secondary's expectation, and what is the patient's true responsibility? Unanswered in both the contract doc and the claims atomic, and it is where patient statements most often go wrong.
9. **The gross-versus-net seam.** Receipts posted at gross can never tie to a net-funded processor deposit; the difference is a fee, not an error, and no object in the model carries a fee at batch level.
10. **The statement-versus-dispute seam.** Statementing a patient for PR while the practice is disputing the payer's adjudication generates refunds, complaints, and churn. No rule holds a statement while an open variance touches its claim.
11. **The non-patient-money seam.** Interest, rebates, records fees, settlement checks, and returned refund checks all have no entry point except patient obligations, so they will accumulate as permanent unapplied cash — the exact failure the credit-control feat exists to prevent.

## Intuitiveness assessment — what the owner-physician's money view should be

One page. Three questions, in this order, answered above the fold, in under five minutes a week.

**1. Did the money come in?** One number — collected this week — against a 13-week trailing band, plus tomorrow's predicted deposit. Predicting the deposit is what converts this from a report into a relationship.

**2. What is stuck?** Three rows, each a dollar amount and a count, never a status name: claims sent but never acknowledged, claims adjudicated but not yet deposited, patient balances past their first statement. Each row opens a list where every line has exactly one owner and exactly one next action. If a row has no action, it does not belong on this page.

**3. Does it tie?** One traffic light per bank line for the last 30 days: matched, variance with a named reason, or unexplained. Only unexplained is red. Everything else the finance cluster does is in service of keeping that column green.

Three rules make this work and all three are violated by default in this domain:

- **No status vocabulary reaches this page.** Not "unapplied cash" — "$412 we received but haven't figured out who it belongs to." Not "PLB WO" — "Aetna took back $180 from a claim we billed last November." Not "period close" — "July is printed." The subledger's technical precision is for the ledger; the owner's page is written in English.
- **Every number drills to the rows that compose it, and every screen states its as-of and its exclusions.** The moment the owner cannot reconcile a headline number to a list, they start keeping the real numbers in a spreadsheet, and the product has failed regardless of its correctness.
- **The week ends in zero decisions or one decision.** Anything requiring a judgment call from the owner should be batched into a single weekly digest — the payer drift alarm, the write-off approvals above threshold, the refunds awaiting sign-off — and everything else should be resolved by staff inside the work queues without ever reaching this page.

The bookkeeper's view is a separate, much smaller thing: one screen, one button — "export July" — and one certificate that says collections minus refunds minus fees equals the bank, difference zero.

## Top 3 reframings

1. **Contract expectation → payer promise memory.** Stop modeling agreements and start remembering payments. "What has this payer actually paid us for this code, and has that changed?" is answerable from data the practice already generates, needs no configuration, improves weekly, and absorbs every real-world adjustment the modeled path must be taught one at a time. The contract becomes evidence for an argument, not an executable artifact — which is also the legally safer position.

2. **Period close → the books are always closed; month-end is just printing.** Immutability at post time, posting-date-stamped rows, as-of queries, and versioned export batches deliver everything a close delivers — reproducible historical reporting — with no lock, no ceremony, and no obstacle when a late ERA or a 60-day-old chargeback arrives. It is less code and it is what the customer will actually do.

3. **Reconciliation → every bank line has an owner.** Reconciliation is not a report and not a batch-matching engine; it is a to-zero inbox keyed to lines on the bank statement, where every credit and debit must be explained by exactly one thing and the unexplained ones are red. This reframing collapses deposit matching, EFT-ERA reassociation, merchant fees, chargebacks, PLB takebacks, and returned items into a single finishable task with a single visible definition of done — and it is the version of "financial integrity" the owner-physician can verify with their own bank app.
