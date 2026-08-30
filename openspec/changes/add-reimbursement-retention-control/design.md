## Context

openPractice is currently a governance scaffold with no Frappe application or practice-management data model. Its product boundary includes administrative and financial workflows but excludes signed clinical facts. The first reimbursement lifecycle implementation therefore needs contracts that remain usable with any EHR and without Medx-specific services while still allowing deployment-specific identity, payer-policy, storage, key-management, and public-witness adapters.

Reimbursement authority and record custody have different lifecycles. A patient, workforce member, vendor, or workload can lose permission to use a record while the practice remains obligated to retain it. Conversely, an expired retention period is not by itself permission to disclose or destroy a record. Jurisdiction-specific rules, especially Medicaid durations, cannot be inferred from generic labels or embedded as unreviewed constants.

Stakeholders include practice owners, billing and revenue-cycle staff, privacy and compliance reviewers, legal-hold custodians, auditors, storage and key-management operators, patients and payers receiving authorized disclosures, and implementers of optional policy and public-anchor adapters.

The repository's `.specify/memory/constitution.md` is still an unratified placeholder. Constitution adoption is therefore separate governance work that must finish before this product feature enters planning; replacing that template is not part of the reimbursement feature.

## Goals / Non-Goals

**Goals:**

- Define portable authorization contracts for billing, claim, remittance, and payer-disclosure operations.
- Make each local decision, use event, and authorized local mutation atomic, and make uncertain external effects explicit and reconcilable.
- Produce owner-local, attributable lifecycle evidence for allowed and refused use, retention, hold, disposition, and execution events.
- Represent retention policy and resulting obligations as versioned, sourced, reviewable records.
- Preserve access revocation and custody retention as independent state dimensions.
- Block deletion and key destruction while any applicable obligation or legal/audit hold remains active.
- Support append-only successor obligations for reopening, appeal, audit, investigation, and other approved extensions.
- Limit public witnessing to asynchronous opaque commitments.
- Require public payloads to conform exactly to a selected, version-pinned neutral public-envelope profile while keeping local associations outside that envelope.
- Require unconditional owner ratification before handing product work to exactly one Speckit feature.
- Preserve separate constitution governance, a meaningful plan-phase Constitution Check, and post-merge archival gates.

**Non-Goals:**

- Defining any Medicaid, HIPAA, state, payer, contract, tax, or medical-record retention duration.
- Activating a Medicaid duration profile without all required named scope, source, effective-date, and human-approval inputs.
- Defining signed clinical facts, patient-consent law, wallet protocols, storage deletion mechanics, key-management mechanics, or public-chain adapters.
- Modifying another repository's runtime, adopting the repository constitution inside this product feature, or treating OpenSpec governance tasks as Speckit implementation tasks.
- Treating access revocation as disposition authority or a hold as permission to access data.
- Claiming that a provider destruction event proves deletion from every backup, replica, derivative, plaintext location, or unknown key copy.
- Publishing patient, payer, claim, program, operation, or retention metadata on a public anchor.

## Decisions

### 1. Keep the portable core adapter-neutral

The openPractice core will define operation categories, decision inputs and outcomes, policy-profile prerequisites, obligation and hold state, disposition evaluation, and lifecycle event semantics. Identity/wallet systems, jurisdictional policy packages, storage providers, KMS providers, and public-anchor services will integrate through bounded contracts and adapters. No Medx product or specific EHR will be required for core behavior, and the single product feature will not modify another repository's runtime.

This is preferred over embedding Medx or payer-specific contracts because openPractice must remain independently usable and because legal policy content has a different approval and release lifecycle from application behavior.

### 2. Authorize each protected operation before side effects

Each billing, claim, remittance, or payer-disclosure attempt will be classified into a protected operation and evaluated against current actor or workload identity, organization role and financial authority, purpose, payer/program profile, object scope, and recipient/output scope where applicable. Missing, expired, revoked, or mismatched authority will fail closed before the protected side effect. Both allowed and refused attempts will create owner-local decision events.

This is preferred over a broad billing-session grant because claim correction, remittance access, and payer disclosure have different recipients, impacts, and least-privilege needs.

### 3. Commit local outcomes atomically and external effects through an idempotent outbox

For an entirely owner-local protected operation, the authorization decision, owner-local use event, and authorized local mutation will commit in one transaction. If any write or invariant check fails, all three roll back and the caller receives failure rather than success. A refusal will be returned only after its decision and refusal event are durably recorded; failure to persist that evidence is an operational failure, not a successful policy refusal.

For a payer, clearinghouse, payment, or other external effect, one local transaction will persist the authorization decision, a conforming attributable intent/use event, the pending local mutation, and the outbox item before dispatch. The intent/use event will carry every field and decision binding required of other owner-local use events: local object references, actor or workload identity, requested operation, purpose, relevant authority and policy versions, authorization decision reference, decision outcome and reason, and event time. It will also link locally to the external intent and its outbox item. The outbox will carry a stable, owner-local idempotency key for that external intent and reuse it for retries.

External acknowledgement or provider query is reconciliation evidence, not a mutation of the original intent/use event. Each reconciliation result will produce a separate, attributable owner-local outcome event linked to the original intent/use event, authorization decision, external intent, and idempotency key. The outcome event will identify the provider, reconciliation evidence reference, result reason, event time, and one of `success`, `failure`, or `indeterminate`. Persisting that outcome event and the corresponding local state transition will be atomic. An inconclusive reconciliation appends an indeterminate outcome and leaves reconciliation required; a later conclusive result appends a new linked terminal outcome without rewriting prior events.

Success will not be reported until a linked success outcome event and successful local state are durable. Confirmed failure will produce a linked failure outcome event before terminal failure is reported. If the external effect may have succeeded but acknowledgement, reconciliation, or outcome-event persistence is missing, the operation will remain or be derived as indeterminate and reconciliation will query or otherwise resolve the provider before any terminal result is asserted.

This is preferred over synchronous dual writes because no database transaction can atomically commit with a payer, clearinghouse, or payment provider. The idempotent outbox and indeterminate state prevent duplicate effects and false success without pretending distributed atomicity.

### 4. Model policy definitions separately from obligation instances

An immutable policy-profile version will identify its authority source, jurisdiction, program, provider type, effective-date scope, covered record families, trigger and calculation rule, supersession semantics, and human approval. Applying an approved version creates an obligation instance bound to the relevant record or record family and the calculation inputs used at that time.

An exact Medicaid duration profile will remain disabled unless it names the jurisdiction, program, provider type, effective date, authoritative source, and human approval. The core will not ship a fallback duration or infer one from a generic Medicaid or HIPAA label.

If no jurisdiction is selected, every jurisdiction-dependent duration calculation remains unresolved and automation cannot produce a disposition horizon. This gate applies before program-specific profile evaluation and cannot be bypassed by a generic payer or record label.

Separating definitions from instances preserves what rule was actually applied and avoids silently changing existing obligations when policy research or approval changes.

### 5. Use append-only successors for lifecycle extensions

Reopening, appeal, audit, investigation, litigation, or another approved event can create a successor obligation linked to its predecessor and trigger evidence. The predecessor remains addressable and is not rewritten. Evaluation uses the current set of applicable obligations, including successors.

This is preferred over mutating an original retention date because the reason and authority for each changed disposition horizon must remain auditable.

### 6. Keep holds independent and make them dominant at disposition

Legal and audit holds will be separately authorized, versioned state records with impose, renew, challenge, and release events. A hold neither grants record access nor changes the access-revocation state. Any active applicable hold blocks both storage deletion and key or wrapper destruction, regardless of an otherwise elapsed obligation.

This is preferred over representing a hold as a long retention duration because a hold has different authority, review, release, and challenge semantics and may not have a known end date.

### 7. Require an explicit disposition decision and scoped execution result

The disposition gate will evaluate every applicable obligation and hold for the requested record-family scope and action. It will refuse authorization unless all obligations are cleared, no applicable hold is active, and an authorized reviewer approves the proposed storage deletion, key or wrapper destruction, or combined action. Execution providers will report outcomes separately from the authorization decision.

A provider destruction event will identify only the requested scope, provider, method category, execution result, time, and related disposition decision. Its semantics are a scoped provider claim, not proof about unobserved backups, replicas, derivatives, plaintext, or key copies.

This is preferred over automatic deletion at the calculated horizon because evaluation errors, newly discovered successors, holds, provider limitations, and operational recovery requirements require an accountable gate.

### 8. Keep evidence owner-local and public witnessing profile-conformant, opaque, and asynchronous

Lifecycle events and their sensitive references will remain in the owner's openPractice environment. When public witnessing is enabled, configuration will select and pin one neutral public-envelope profile version. The serialized public payload will conform exactly to that profile and will not use a locally extended public-envelope superset. It will contain no stable owner-local correlation identifier or claim, payer, patient, program, operation, exact date horizon, or legal-policy label.

The outbox may keep a private association among the owner-local event, exact profile-conformant envelope, dispatch attempt, and receipt, but those association fields are not part of the public envelope and are never serialized into public payloads. Anchor submission, retry, and receipt association will occur asynchronously. Failure or delay of a public witness will not grant authority, change custody state, or block completion of an otherwise valid owner-local decision. Public proof semantics are limited to evidence that committed bytes existed by a witnessed point and have not changed relative to the commitment.

This is preferred over synchronous, descriptive, or locally extended anchoring because public availability is not an authorization dependency, profile drift impairs interoperability, and descriptive or stable correlation fields would leak sensitive relationships and retention facts.

### 9. Gate one Speckit handoff with separate governance, ratification, and closure evidence

The proposal's top-level `Status` field has the controlled values `draft` and `ratified`. It remains `draft` while owner ratification is pending. Only the identified owner may change it to `ratified`, and only in the durable update that completes `owner-ratification.md` for the same strictly validated artifact revision. Agents must not infer, advance, or bypass this field.

Before any Speckit feature is created, this change must pass strict OpenSpec validation, the owner must record an unconditional ratification in `owner-ratification.md`, and the proposal status must be `ratified`. A conditional, delegated, inferred, or agent-authored approval does not satisfy the gate. The durable record must identify the owner, exact ratified artifact revision, timestamp, strict-validation evidence, and unconditional decision.

The repository constitution must be adopted through separate governance work rather than this reimbursement feature. The one Speckit feature may be created only after ratification; before its plan phase proceeds, it must perform a meaningful Constitution Check against the separately adopted, versioned constitution and stop on unresolved violations. The feature's plan must also prove that runtime changes stay inside openPractice and its generated worktree; another repository may be represented only by contracts, test doubles, or documented integration assumptions unless separately governed work is approved there.

This OpenSpec change will hand off once to a single Speckit feature, with its sequence number assigned by the repository's Speckit workflow. That feature will own clarification, implementation planning, executable tasks, analysis, code, and verification for all three capabilities. This OpenSpec change will remain active until that feature's implementation and validation are merged into the intended base branch, final acceptance evidence is recorded, and strict OpenSpec validation passes; only then may it be archived.

This is preferred over separate feature handoffs or embedded governance implementation because authorization, retention evaluation, and evidence semantics share one lifecycle contract, while constitution adoption and repository governance have independent ownership. The shared workflow also prohibits duplicate implementation task lists and premature archival.

## Risks / Trade-offs

- **[Policy data can be incomplete or legally wrong]** → Keep profiles disabled until required provenance and human approval exist; preserve versions and never supply a generic duration fallback.
- **[Fine-grained authorization can slow billing workflows]** → Define stable operation categories and allow future batch scopes without weakening purpose, recipient, object, or output boundaries.
- **[A payer or payment effect can succeed while local acknowledgement fails]** → Atomically persist a fully attributable intent/use event with the outbox, append separate linked reconciliation outcome events, preserve explicit indeterminate state, and never infer or report success from an uncertain response.
- **[A missed obligation or hold can permit premature disposition]** → Fail closed on unknown evaluation state, require an explicit reviewer, and test successor and hold precedence.
- **[Append-only evidence increases sensitive data volume]** → Apply retention controls to use and lifecycle events themselves and keep all descriptive content owner-local.
- **[Asynchronous anchors can be delayed or unavailable]** → Persist retryable outbox state locally and make authorization and custody decisions independent of public-anchor availability.
- **[Public payloads can drift or become correlatable through local convenience fields]** → Pin a neutral public-envelope profile version, serialize only its exact schema, and store private associations separately from the envelope.
- **[Cryptographic erasure can be overstated]** → Separate disposition authorization from provider execution and constrain destruction events to explicit, provider-observed scope.
- **[Greenfield contracts may not map cleanly to the eventual Frappe model]** → Resolve DocType, permission, signing, and transaction boundaries during the single Speckit design cycle without changing these behavioral requirements.
- **[Governance could be bypassed to start implementation quickly]** → Block feature creation on durable unconditional owner ratification, block planning on a separately adopted constitution and meaningful check, and block archival on merged implementation plus accepted validation.

## Migration Plan

1. Strictly validate this OpenSpec change and obtain the owner's durable, unconditional ratification for the exact artifact revision.
2. Create exactly one Speckit feature and complete clarification without beginning the plan phase.
3. Adopt and ratify the repository constitution through separate governance work; the current placeholder does not satisfy the plan prerequisite.
4. Gate the plan phase on a meaningful Constitution Check and an explicit no-cross-repository-runtime boundary.
5. Establish the first openPractice data contracts, permissions, atomic local transaction boundary, conforming intent/use event plus idempotent external-effect outbox transaction, and separate linked reconciliation outcome events for `success`, `failure`, and `indeterminate` states.
6. Introduce retention policy profiles with jurisdiction-unselected automation blocked and all exact Medicaid profiles disabled by default; activate only separately researched and approved profiles in deployment data.
7. Introduce obligation, successor, hold, and disposition evaluation before enabling any storage or key executor.
8. Add optional public-anchor processing only after selecting and pinning a neutral public-envelope profile and verifying exact payload conformance, private association separation, and retry behavior.
9. Roll back by disabling external-effect, executor, and anchor adapters while preserving owner-local decision, obligation, hold, reconciliation, and evidence history for audit. Do not roll back by deleting evidence, reporting uncertain success, or bypassing the disposition gate.
10. Archive this change only after the single feature's implementation and validation are merged, acceptance evidence is recorded, and strict OpenSpec validation passes.

## Open Questions

- Which concrete Frappe DocType boundaries and transaction boundaries best preserve atomic decision and event recording?
- Which local signing or integrity mechanism will be available in the first openPractice runtime?
- Which external payer, clearinghouse, and payment APIs provide idempotency and authoritative reconciliation queries for the first implementation?
- Which roles may impose, renew, challenge, release, and review holds in the first deployment profile?
- Which storage/KMS provider result vocabulary can report partial, delayed, or failed execution without overstating destruction?
- Which neutral public-envelope profile and exact version, if any, is selected for the first optional public-anchor adapter? The core contract remains anchor-neutral and public witnessing stays disabled without a selection.
