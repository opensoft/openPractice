# Framing Decisions: The Eleven Calls Before Any Rewrite

Status: brainstorm
Kind: decision-brief
Summary: the eleven framing decisions surfaced by the feat-review synthesis, each worked to a recommendation with rationale, counter-considerations, and downstream consequences — awaiting ratification before Wave 1 (paper prototypes) and Wave 2 (governing-document rewrites) proceed.
Topics: openpractice-feat-review, framing-decisions
Captured: 2026-08-26

Each decision below is worked to a **recommendation**. **All eleven were
ratified as recommended by the product owner on 2026-08-27** (including the
modifications noted inline). These decisions now govern the Wave 1 paper
prototypes and the Wave 2 rewrites of the governing documents.

---

## FD-1. Staff-count frame

**Decision:** Who is the product designed for, and is that a persona or a range?

**Recommendation — accept.** Fix the design frame at **2–4 in-house staff**, and
model the **outsourced biller as a first-class external actor** on the Roster
(bounded access, own work views, own accountability), since many small practices
outsource RCM rather than employ a biller.

**Why:** Every "questionable" verdict in the review traced to one source —
configuration surfaces asking the practice to author policy it has never
articulated. A fixed frame deletes that entire class: no routing engines, no
approval ladders, no RBAC matrix, no config-promotion pipeline.

**Watch-out:** Don't design a ceiling into the schema. The frame governs
**defaults and surfaces**, not data structures. Growth beyond the frame adds
machinery later; it never requires redesigning the core objects.

**Changes:** feature-normalization gains a "frame rule"; every atomic drops its
enterprise-surface variants; the Roster atomic gains the external-biller actor.

---

## FD-2. Expectation source (learned vs. contract)

**Decision:** Where does "what should this payer pay us" come from?

**Recommendation — accept (invert the current default).** The **learned
baseline is primary**: expected allowables are a statistic over the practice's
own posted remittances (median by payer × code × modifier × POS, with n and
spread). The **contract is the arbiter**, loaded only when the practice wants to
dispute — and *disagreement between promise-memory and contract is itself the
highest-value alert*.

**Why:** Zero configuration, self-improving, absorbs sequestration and mid-year
fee changes for free. Nobody at a 2–4 person practice will load and maintain
fee schedules; a contract-first engine is a contract-management product nobody
will populate.

**Watch-out — cold start:** at go-live there is no history. Mitigate in order:
seed from migrated EOB/ERA history where available; use the public Medicare fee
schedule as a reference band; and let variance detection activate progressively
as n grows rather than pretending confidence at n=1.

**Changes:** payer-contract-and-reimbursement-integrity is rewritten around
promise memory; the "governed reimbursement terms" feat demotes to an optional
arbiter import; the expectation engine leaves the beta's critical path.

---

## FD-3. Period close

**Decision:** Is there a close ceremony, or are the books always closed?

**Recommendation — accept.** **Immutability at post time** (corrections are
dated reversals, never edits) + **as-of queries** + the **daily balanced day**
from End-of-Day Close. Month-end is printing, not a procedure.

**Why:** No small practice runs a close ceremony, so a ceremony either blocks
the product or gets skipped — both fatal. Reversal-based correction preserves
full auditability without a lock.

**Watch-out:** The accountant still needs a stable artifact. **Export-batch
freeze** provides it: once a journal batch is exported to QuickBooks, that batch
is versioned and immutable; later corrections land in the next batch with
back-references. The accountant gets stability; the practice never sees a lock.

**Changes:** financial-control-and-accounting-bridge drops the "controlled close
and correction" ceremony; End-of-Day Close (new atomic) becomes the daily
integrity unit; accounting export gains batch versioning.

---

## FD-4. Estimates and GFE

**Decision:** Do estimates, self-pay, and the Good Faith Estimate enter the beta?

**Recommendation — accept (promote to beta-scoped money path).** One
**self-pay / time-of-service money path**: estimate → GFE document where
required → collect at visit → receipt → thread closed same day. The GFE
register (issued GFEs, amounts, dispute window) rides the same path.

**Why:** This was the weakest treatment in the packet — as sequenced, the beta
collects no money before or at the visit, and cash patients sit entirely outside
the integrity model. The GFE is a legal obligation for uninsured/self-pay
patients, not a feature choice. It is also the *simplest* money path — no payer,
no clearinghouse — so it's the natural first proof of the money model.

**Watch-out:** Keep estimate sophistication out of scope: beta estimates are
schedule-of-charges arithmetic plus promise-memory copay/deductible hints, not
benefit-engine modeling.

**Changes:** scope-and-sequencing gains the self-pay path in an early slice;
patient-billing atomic gains time-of-service collection; a new GFE register
feat lands in the documents/attachments or billing atomic.

---

## FD-5. Governance primitive

**Decision:** What replaces the human-approval gate?

**Recommendation — accept, with a constitutional carve-out.** Replace approval
with **reversibility, visibility, and delay**, shipped as four fixed tiers (no
tier authoring):

1. **Do + undo** — reversible, silent, undoable from the record.
2. **Do + notify** — reversible, surfaced in Today/close.
3. **Delay + cancel window** — outbound actions (claim batches, statements,
   outreach) execute after a visible countdown unless cancelled.
4. **Hard stop** — true approval, reserved for the constitutionally
   irreversible few: refunds leaving the practice, material adjustments,
   record merges.

**Every gate carries a deadline escape:** a gate that would stall a claim past
timely filing auto-releases at tier 3 with notification, because the gate
otherwise destroys what it protects.

**Why:** The second human does not exist; approval queues rot within a month.
All five reviewers independently converged on this replacement. Tier 4
preserves the constitution's "no autonomous money movement" line.

**Changes:** deletes the generic approval framework; resolves the
timely-filing conflict outright; every atomic's "human confirms" language is
rewritten against the four tiers.

---

## FD-6. Differentiator statement

**Decision:** What is provenance *for*?

**Recommendation — accept.** Provenance is restated as **generated artifacts**:
the one-click appeal packet, the auto-derived secondary claim, the
retro-rebill list, the stuck-money worklist, the printed audit binder, and
**undo**. Rule: every provenance link must name the artifact it emits and the
minutes it saves — if none can be named, store less. The queryable API remains
as substrate, not as the headline and not as the build order.

**Why:** A 2–4 person practice never calls a trace API. Artifacts are the same
data made valuable — and undo (FD-5) is itself a provenance artifact, which
makes the two decisions one architecture.

**Changes:** feature-list-overview's three differentiator claims are restated;
the provenance viewer and the AI-specific decision ledger are deleted; build
order starts from the artifact list.

---

## FD-7. Spine ownership

**Decision:** Which of the four competing identity spines owns correlation?

**Recommendation — accept.** **One thread identity.** The Visit Thread is
written as a versioned contract **before any consumer**, and the four current
descriptions (revenue cycle's financial thread, platform's transaction trace,
platform's correlation-as-data, the AI decision record) collapse into views
over it. Non-visit threads (credit balance, unmatched referral, recoupment
case, migrated balance) are first-class and enumerated.

**Why:** Four spines means provenance built twice and joined never. The
reviewers flagged this as "one of the two must be authoritative or both will be
half-built."

**Changes:** The Visit Thread atomic is pulled forward into Wave 3 as the first
document written; platform's trace/correlation feats become requirements on the
Thread contract rather than separate builds.

---

## FD-8. First external milestone

**Decision:** What must be live (not test traffic) for the beta to count?

**Recommendation — accept.** Beta acceptance requires **one live ERA posted
end-to-end and one live merchant settlement reconciled to a bank line**. The
test-traffic escape is retired.

**Why:** Test traffic proves plumbing; the product's two hardest external
dependencies (clearinghouse enrollment, merchant settlement data) are exactly
where healthcare betas die, and deferring them defers discovering the product
doesn't work.

**Watch-out — critical path:** clearinghouse and merchant enrollment take
weeks and are partner-paced. Both enrollments start at beta kickoff, tracked as
the implementation's first two threads.

**Changes:** scope-and-sequencing's acceptance criteria are rewritten; the
implementation-lifecycle atomic gains "enrollment as first threads."

---

## FD-9. Cutover

**Decision:** How does a practice move from its legacy system?

**Recommendation — accept, with one addition.** **Progressive activation**
replaces the cutover project: schedule → eligibility → charges → claims, each
activated when its "first" succeeds. Migration is scoped to **open items only**
(future appointments, active patients on the schedule, open balances as summary
threads). Legacy AR **runs off in the legacy system by default**.

**Addition:** an **"old-system balance" thread type**, so the owner's money
view still shows whole-practice truth (openPractice money + legacy run-off) and
the run-off is watched work, not invisible work.

**Why:** Full AR conversion is the classic implementation killer — highest
effort, highest error rate, lowest durable value. Open-items scoping bounds
risk to what the practice actually needs on day one.

**Changes:** implementation-and-configuration-lifecycle drops cutover control
room, config promotion, and policy simulation; gains progressive activation and
the six "firsts"; the Thread contract gains the migrated-balance type.

---

## FD-10. Channels

**Decision:** Do phone and fax get owned, and what does app deferral not defer?

**Recommendation — accept both halves.** (a) **Phone and fax become owned
atomics** — the Phone Console / call-disposition capture and the fax side of
referral intake (confirmed-destination registry, inbound fax → Pile). The
system diagram and the feature set finally agree. (b) The companion app stays
deferred, but the **patient money view does not**: balance + pay-now behind a
texted link, no account required, is beta scope (it is the payments half of
FD-4's money path).

**Why:** Phone is the dominant small-practice channel and is currently
invisible, making every queue and metric structurally wrong from week one. Fax
is how referrals actually arrive. The texted pay link is where patient dollars
actually come from.

**Changes:** two new Tier-1 atomics (phone, inbound pile/fax); engagement and
referral atomics point at them; patient-billing gains the no-account pay link.

---

## FD-11. Licensed IP

**Decision:** How do licensed code sets (CPT®) and X12 implementation guides
ship in an open-source product?

**Recommendation — accept the shape; verify with counsel.**
**License-free core + licensed overlay, never licensed content in the repo:**

- The open-source core ships only license-free content: ICD-10, HCPCS Level II,
  place-of-service, CARC/RARC, and the *structure* for procedural coding.
- CPT ships as a **bring-your-own-license overlay pack**: first-run import from
  the practice's own licensed source (practices billing CPT already hold usage
  rights through their coding workflow; the import formalizes it).
- X12 knowledge is **abstracted behind the clearinghouse connector** so the
  product need not redistribute implementation guides; if a direct-to-payer
  path ever ships, it ships as a hosted service that carries the license.
- A future hosted offering may bundle licensing commercially; the repo never
  does.

**Why:** This is the only shape that preserves open-source distribution without
either shipping unlicensed IP or making the product unusable out of the box.
It also cleanly forks the onboarding story: first-run setup includes "load your
code sets," which FD-9's progressive activation already accommodates.

**Watch-out:** AMA licensing terms for practice-held CPT rights need legal
verification before the onboarding flow is finalized. Track as an explicit
open risk in scope-and-sequencing.

**Changes:** feature-normalization gains the licensing rule; charge-capture's
code-catalog feat is split core/overlay; implementation-lifecycle gains the
code-set import first-run step.

---

## Ratification record

| # | Decision | Status |
|---|----------|--------|
| FD-1 | Staff-count frame: 2–4 + external biller | ratified 2026-08-27 |
| FD-2 | Learned baseline primary, contract as arbiter | ratified 2026-08-27 |
| FD-3 | Always-closed books, export-batch freeze | ratified 2026-08-27 |
| FD-4 | Self-pay/GFE money path in beta | ratified 2026-08-27 |
| FD-5 | Reversibility/visibility/delay, 4 tiers, deadline escapes | ratified 2026-08-27 |
| FD-6 | Provenance as generated artifacts | ratified 2026-08-27 |
| FD-7 | One Thread identity, contract-first | ratified 2026-08-27 |
| FD-8 | Live ERA + live settlement as acceptance | ratified 2026-08-27 |
| FD-9 | Progressive activation, open-items migration | ratified 2026-08-27 |
| FD-10 | Phone/fax owned; patient money view not deferred | ratified 2026-08-27 |
| FD-11 | License-free core + licensed overlay | ratified 2026-08-27 |

## Relationships

Derived from [Feat Review Synthesis](openpractice-feat-review-synthesis.md)
§7(c). Consumes findings from all six cluster reviews in this directory. Once
ratified, governs the Wave 1 paper prototypes and the Wave 2 rewrites of
[Scope And Sequencing](../brainstorm/openpractice-small-practice-beta-scope-and-sequencing.md)
and [Feature Normalization](../brainstorm/openpractice-small-practice-beta-feature-normalization.md).
