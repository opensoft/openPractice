# Implementation And Configuration Lifecycle — Brainstorm

Status: brainstorm
Kind: process
Summary: openPractice should make small-practice implementation a versioned, rehearsable workflow from discovery and mapping through configuration, migration, cutover, stabilization, rollback, and evidence-backed acceptance rather than a collection of undocumented administrator changes.
Topics: openpractice-small-practice-beta, implementation-lifecycle, interoperability, operations, feature-roadmap
Repository context: openPractice — setup, migration, and configuration operations for the small ambulatory practice beta
Captured: 2026-08-26

## Possible feats

- **Guided practice bootstrap** — create a minimal organization, locations, staff roles, providers, offerings, calendars, payers, channels, and financial controls from a small-practice template.
- **Versioned configuration set** — package domain settings, source evidence, approvals, effective dates, dependencies, and compatibility so changes can be reviewed and reproduced.
- **Migration rehearsal** — import declared source exports into a non-production run, preserve row-level provenance and rejects, and produce a reconciliation report before cutover.
- **Policy simulation** — test scheduling, readiness, communication, authorization, claim-edit, routing, and financial rules against synthetic scenarios before activation.
- **Cutover control room** — track prerequisites, owners, data freeze, final deltas, connector readiness, acceptance evidence, fallback, and post-live stabilization.
- **Configuration promotion and rollback** — move reviewed configuration among development, test, training, and production without hand-editing or rewriting historical behavior.

## Focus

How can a small practice adopt openPractice without a large implementation team while retaining safe, auditable control over data mappings, policies, integrations, and go-live decisions?

## Proposed model

Implementation is a domain workflow with its own artifacts and states:

```text
discover → design and map → configure → dry-run import → reconcile
        → rehearse workflows → approve cutover → migrate final delta
        → activate → stabilize → accept or roll back
```

A **Practice Configuration Set** identifies versions of organization structure, roles, service offerings, calendars, payer and connector settings, communication purposes and templates, readiness policies, financial authorities, metric versions, retention controls, and adapter mappings. It records authors, reviewers, source evidence, validation results, effective dates, dependencies, secrets by reference, and promotion history. Secrets remain in a protected runtime store and never enter the versioned package or repository.

A **Migration Run** records source type and version, declared export or API contract, mapping set, synthetic or authorized environment, row and artifact counts, hashes, accepted and rejected records, corrections, unresolved conflicts, reconciliation totals, and approving actor. Repeating the same run with the same inputs and mapping should produce the same result or an explicit version difference. Patient identity merges, financial opening balances, and document ownership conflicts always require governed review.

The beta setup experience should reveal advanced structure only when needed. A one-location template can configure sensible defaults quickly while the underlying data remains effective-dated and capable of adding another location. Readiness checks explain missing configuration in practice language: which appointments cannot be booked, which providers cannot be billed, which payer transactions have no route, which messages lack a lawful or recorded purpose, and which balances cannot reconcile.

## Interfaces and boundaries

Consumes explicit practice decisions, source-system exports or APIs, mapping definitions, connector capabilities, role and security policy, synthetic validation scenarios, and human approvals. Emits versioned configuration, import provenance, rejects, reconciliation results, setup and cutover work, operational readiness, and acceptance evidence to every domain.

It does not treat a spreadsheet import as authoritative without validation, embed real credentials in configuration artifacts, read another application's database directly, silently merge identities, alter posted financial history, or claim that a technically successful installation satisfies every legal or operational obligation. Training content, managed implementation services, and marketplace distribution may build on this workflow but are separate concerns.

## Alternatives and tensions

Direct administrator configuration is fastest for a prototype but produces irreproducible environments and unexplained drift. Fully declarative configuration improves review and portability yet may be awkward for ordinary front-office administrators; guided UI changes can generate the governed set underneath. A one-time migration minimizes tooling effort but leaves corrections and future practice acquisitions unsupported. Rollback is straightforward for configuration and code, but financial transactions and externally transmitted claims require compensating actions rather than destructive reversal.

## Open questions

- Which source system and data classes form the first supported migration path?
- What configuration is global, practice-wide, location-specific, payer-specific, provider-specific, or environment-specific in the beta?
- Which changes can activate immediately, which require future effective dates, and which require a formal promotion window?
- What constitutes beta cutover acceptance for identity counts, schedules, balances, documents, integrations, security, recovery, and staff readiness?
- How much of setup should be template-driven versus discovered interactively from the design partner?

## Relationships

Combined with [Practice Structure And Service Catalog](openpractice-small-practice-beta-practice-structure-and-service-catalog.md), [Provider Credentialing, Enrollment, And Directory Lifecycle](openpractice-small-practice-beta-provider-enrollment-and-directory.md), [Referral Administration](openpractice-small-practice-beta-referral-administration.md), [Administrative Documents And Claims Attachments](openpractice-small-practice-beta-administrative-documents-and-attachments.md), and [Access Equity And Channel Parity](openpractice-small-practice-beta-access-equity-and-channel-parity.md) in [Synthesis: Small-Practice Administration](openpractice-small-practice-beta-synthesis-practice-administration.md). Its release boundary is constrained by [Small-Practice Beta Scope And Sequencing](openpractice-small-practice-beta-scope-and-sequencing.md).
