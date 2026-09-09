<!--
SYNC IMPACT REPORT
==================
Version change: (none — initial adoption from template) → 1.0.0

Modified principles: n/a (initial adoption)

Added principles:
  - I. HIPAA Compliance by Design (NON-NEGOTIABLE)
  - II. Security Review Gate for PHI and Financial Flows
  - III. Test-First Development (NON-NEGOTIABLE)
  - IV. Clinical Boundary & Original Implementation
  - V. Versioned Integration Contracts Only
  - VI. Deployment Portability

Added sections:
  - Technology Stack Constraints
  - Development Workflow & Quality Gates
  - Governance

Removed sections: none (all template placeholders resolved)

Templates status:
  - .specify/templates/plan-template.md        ✅ reviewed — Constitution Check
    section intentionally stays generic; gates are derived per feature from
    this file at plan time.
  - .specify/templates/spec-template.md        ✅ reviewed — technology-agnostic;
    no conflicts with principles.
  - .specify/templates/tasks-template.md       ✅ updated — test tasks changed
    from OPTIONAL to MANDATORY to comply with Principle III.
  - .specify/templates/checklist-template.md   ✅ reviewed — generic, no
    constitution references requiring updates.
  - .specify/templates/commands/               ✅ not present in this repo; skipped.

Follow-up TODOs: none deferred.
-->

# openPractice Constitution

## Core Principles

### I. HIPAA Compliance by Design (NON-NEGOTIABLE)

Every feature that creates, receives, maintains, or transmits Protected Health
Information (PHI) MUST implement the following safeguards by default, as part
of the feature itself rather than as later hardening:

- Encryption in transit (TLS 1.2+) and encryption at rest for all PHI storage.
- Audit logging of every PHI access: who accessed what, when, and from where,
  written in a form suitable for tamper-evident retention.
- Minimum-necessary role-based access control: each role sees only the data its
  function requires.
- Session controls including automatic logoff on idle.

PHI MUST never appear in source code, test fixtures, seed data, log output,
documentation examples, or commit history. All development and testing uses
synthetic or explicitly de-identified data only.

**Rationale**: openPractice is a medical practice operations platform handling
protected health information and financial data. Retrofitting HIPAA safeguards
after features ship produces unreliable compliance; safeguards must be a design
invariant enforced at specification, planning, and review time.

### II. Security Review Gate for PHI and Financial Flows

A change that touches PHI, financial transactions, authorization decisions,
audit behavior, or integration credentials MUST pass a documented security
review before merge. The review verifies safeguard coverage under Principle I
and checks that the change does not weaken existing controls.

The repository maintains a living security risk analysis (aligned with the
HIPAA Security Rule risk-analysis requirement, 45 CFR §164.308(a)(1)(ii)(A)).
Every feature that changes the attack surface MUST update it in the same change.

Authorization integrity, financial authority, reversibility, provenance, and
auditability are acceptance criteria for operational features — not optional
polish. No autonomous financial or patient-impacting action may be enabled by
default; such actions require explicit human approval flows.

**Rationale**: HIPAA requires ongoing risk management, not a one-time audit.
Encoding the gate into the merge workflow makes compliance continuous and
verifiable instead of aspirational.

### III. Test-First Development (NON-NEGOTIABLE)

All production code is written test-first: tests are written and approved
before implementation, confirmed to fail, then made to pass
(red-green-refactor). No exception based on perceived simplicity.

Additional mandatory testing requirements:

- Contract tests are required for every integration boundary (openChart,
  OpenEMR, MedxFactory, payer/clearinghouse gateways) against the versioned
  contracts described in Principle V.
- Claims, billing, payment, and charge-capture logic MUST include tests proving
  calculation correctness, including boundary and failure cases, before merge.
- Tests MUST NOT depend on real PHI or live external services; they use
  synthetic fixtures and contract doubles.

**Rationale**: In practice-management software, a silent calculation error or
regression can misbill patients, deny claims, or corrupt financial records.
Test-first discipline is the cheapest reliable defense, and uniform enforcement
avoids judgment calls about which code "matters enough."

### IV. Clinical Boundary & Original Implementation

openPractice owns administrative and financial workflows only: registration,
scheduling, coverage, authorizations, charge capture, claims, remittance,
patient billing, payments, operational tasks, and practice reporting. It MUST
NOT own signed clinical facts, encounters, medication lists, results, or
orders; those belong to openChart or another connected EHR.

openPractice is an original implementation. OpenEMR, Marley/Frappe Healthcare,
other practice-management products, and healthcare standards MAY inform
requirements and interoperability behavior, but their code MUST NOT be copied,
forked, or used as an implementation base.

**Rationale**: The product boundary keeps clinical truth authoritative in the
EHR and prevents divergent clinical records; the originality rule preserves
license independence and architectural freedom.

### V. Versioned Integration Contracts Only

Integration with external systems occurs exclusively through explicit,
versioned API contracts:

- FHIR R4 is preferred where an applicable profile exists (e.g., patient,
  scheduling, coverage exchange).
- Otherwise, versioned REST APIs and webhooks with published schemas.
- Direct database access to another system's schema is prohibited in both
  directions.
- Contract changes are additive within a version; breaking changes require a
  new contract version and a documented migration path.

External automation platforms (including MedxFactory) integrate through
bounded, capability-aware contracts: automation scopes are explicit, revocable,
audited, and never grant blanket system access.

The same discipline applies inside the product: Flutter clients communicate
with the Frappe backend through the same versioned API layer, not through
bypasses or undocumented internals.

**Rationale**: Healthcare deployments mix systems with independent release
cadences. Versioned contracts keep upgrades safe and auditable, make every
integration testable under Principle III, and prevent fragile coupling.

### VI. Deployment Portability

The application MUST run on any standard Frappe bench deployment:
practice-hosted servers, MSP-managed infrastructure, private cloud, or managed
cloud platforms. On-premise/self-hosted parity is a first-class requirement,
not a best-effort port.

Constraints that follow:

- Core code MUST NOT depend on proprietary single-vendor cloud services;
  environment-specific capabilities enter behind configuration and adapters.
- Configuration MUST distinguish environments without code changes.
- Deployment documentation MUST allow an operator without vendor relationships
  to install and operate the system.

**Rationale**: Medical practices vary widely in hosting capability and data
residency needs, and BAA obligations differ by host. Portability protects
deployers from lock-in and keeps hosting choices compatible with each covered
entity's compliance posture.

## Technology Stack Constraints

These constraints implement Principles IV–VI at the stack level:

- **Backend**: Frappe framework (Python). The Frappe application/package name
  is `open_practice`. Data modeling follows Frappe DocType conventions;
  schema changes use standard Frappe migration tooling.
- **Frontend**: Flutter applications deliver staff-facing and patient-facing
  experiences. Frappe Desk remains available for system administration,
  configuration, and back-office operations.
- **API layer**: All Flutter-to-backend communication goes through the same
  versioned API contracts required by Principle V.
- **Interoperability**: FHIR R4 resources are the default interchange format
  for cross-system clinical-adjacent data (patients, coverage, scheduling);
  X12/EDI formats apply to payer transactions where industry practice requires.
- **Runtime commands**: Build, test, and lint commands are defined here in
  AGENTS.md as the first governed implementation establishes them; until then
  no runtime is assumed.

## Development Workflow & Quality Gates

Product and architecture changes follow spec-driven development using OpenSpec
for governance and Spec Kit for feature specification, planning, task
generation, implementation, and analysis. Specs and code live together so each
implementation commit traces to requirements and tests.

Quality gates applied per feature:

1. **Specification gate**: Feature spec exists and has passed clarification;
   PHI-handling features identify their Principle I safeguard plan explicitly.
2. **Planning gate**: Implementation plan passes the Constitution Check derived
   from this file; violations require recorded justification in the plan's
   Complexity Tracking table.
3. **Test-first gate**: Tests exist, were written first, and failed before
   implementation (Principle III).
4. **Security gate**: Security review completed for anything touching PHI,
   money, authorizations, or credentials; risk analysis updated (Principle II).
5. **Data gate**: No real PHI, payer credentials, financial account data,
   decrypted records, wallet secrets, or encryption keys in any commit.

A feature is done only when all applicable gates pass and its tests are green.

## Governance

- **Authority**: The repository owner (solo maintainer) ratifies this
  constitution and its amendments. Contributors and AI agents propose changes
  through pull requests; ratification is recorded by merge.
- **Amendment procedure**: Amendments modify this file directly via PR with a
  rationale in the description and an updated Sync Impact Report comment.
- **Versioning**: This constitution follows semantic versioning.
  MAJOR — principle removal, redefinition, or materially incompatible
  governance change. MINOR — new principle/section or material expansion of
  existing guidance. PATCH — clarifications, wording, and non-semantic fixes.
- **Supremacy**: This constitution supersedes conflicting practices,
  documentation, and ad-hoc decisions. Specifications, plans, and tasks MUST
  comply with it; `speckit-analyze` verifies cross-artifact consistency.
- **Compliance review**: Every MAJOR or MINOR amendment includes an explicit
  statement that HIPAA posture (Principles I–II) is preserved or improved.
- **Dates**: Ratified and last-amended dates use ISO 8601 (YYYY-MM-DD).

**Version**: 1.0.0 | **Ratified**: 2026-08-24 | **Last Amended**: 2026-08-24
