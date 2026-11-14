# Platform Security And Compliance — Brainstorm

Status: brainstorm
Kind: report
Summary: openPractice should turn HIPAA safeguards, privacy operations, and recoverability into a portable control plane that continuously proves minimum-necessary access and trustworthy handling of administrative and financial PHI instead of leaving compliance as an operator checklist.
Topics: openpractice-feature-list, platform-security, competitive-research
Repository context: openPractice — Frappe-native practice-operations foundation; feature research versus the OpenEMR practice-management baseline and the clinic/group-practice leaders (athenahealth, eClinicalWorks, AdvancedMD; expanded set NextGen Healthcare, Tebra/Kareo)
Captured: 2026-08-25

## Possible feats

- **Minimum-necessary authority designer** — a matrix editor maps staff, service, location, object, field, and action permissions across administrative and financial records, with risky combinations highlighted before activation.
- **Tamper-evident PHI ledger** — append-only, integrity-chained events capture actor, purpose, object, action, outcome, source, and correlation context for every PHI-touching path, including APIs and automation.
- **Reviewed emergency access** — time-boxed break-glass elevation requires a reason, raises immediate alerts, records all activity, and opens a mandatory post-event review instead of silently granting a superuser session.
- **Identity and session policy center** — role-sensitive MFA, idle timeout, reauthentication, device trust, and concurrent-session limits are enforced consistently across Flutter, Desk, and APIs.
- **Operational privacy workspace** — vendor/BAA registry, privacy-incident register, breach-risk worksheet, accounting-of-disclosures, and access-review campaigns share evidence without pretending software makes legal determinations.
- **Continuous safeguard verification** — scheduled TLS and at-rest encryption checks, sensitive-field protection tests for SSNs and bank details, backup verification, and restore drills produce assigned exceptions with due dates.
- **Auditor-ready evidence without PHI sprawl** — a redacted-log viewer and bounded evidence-pack generator expose controls, approvals, integrity proofs, and drill results while minimizing identifiers and raw record content.

## Focus

How can openPractice ship HIPAA-by-design as an inspectable operating system for trust, rather than reproduce the installation, hardening, backup, and evidence-assembly burden of a self-managed practice platform?

## Current state: OpenEMR baseline

OpenEMR provides granular phpGACL-style role permissions, emergency-login access, MFA, session controls, configurable database/document encryption, audit-event capture and reporting, APIs, and deployable self-hosted packages. Its security guidance still assigns operators substantial work across TLS certificates, web server, database, patches, encrypted storage, backups, and risk assessment; audit and access controls exist, but hardening, retention architecture, review cadence, and recovery assurance remain deployment responsibilities. The reviewed project materials do not present a bundled BAA registry, privacy-incident and breach-assessment workflow, accounting-of-disclosures workspace, or recurring access-certification campaign as a unified control plane.

Sources: OpenEMR Audit Control, Securing OpenEMR, and Release Features wikis plus API authentication/FHIR R4 documentation; athenahealth Developer Portal and authorization guide; eClinicalWorks practice-management integrations and published certification disclosures; AdvancedMD security and cloud-platform pages; NextGen API, Mirth, and FQHC materials; Tebra Why Tebra platform page.

## Enterprise gap candidates

- athenahealth's multi-tenant cloud and scoped OAuth/API program illustrate centrally operated access boundaries and integration governance; its API materials expose authorization, access-control, audit, and encrypted-transport concerns as platform behavior rather than local assembly.
- eClinicalWorks markets secure cloud delivery, user-action-log exports, and FHIR/healow developer surfaces; its published certification disclosures describe auditable events, tamper resistance, and audit reports, showing the evidence expectations relevant when a module pursues applicable ONC criteria.
- AdvancedMD couples cloud operations with role-based access, MFA, audit/event monitoring, incident management, verified backups, and disaster-recovery exercises; the managed posture reduces local operational burden but is not a portability model.
- NextGen combines enterprise APIs and self-service app authorization with managed or self-hosted interface options and long-running community-health-center deployments, raising the bar for delegated governance across large, multi-site organizations.
- Tebra/Kareo's cloud-centered operating model emphasizes vendor-run practice automation for independent practices; openPractice's counter-position is equivalent control quality with first-class on-premise and MSP-managed parity, not a cloud-only dependency.

## Proposed feature set for openPractice

Parity floor: granular admin/financial ACLs, MFA, idle logoff, secure sessions, encryption in transit and at rest, protected audit records, emergency access, and portable backup/restore tooling on any standard Frappe bench. Adopted gaps: a minimum-necessary RBAC matrix editor; field-level protection for SSNs and bank details; integrity-chained append-only auditing across UI, API, and automation; time-boxed break-glass with alerts and review; configurable reauthentication and concurrent-session controls; encryption verification jobs; vendor/BAA and privacy-incident registers; a human-owned breach-risk worksheet; accounting-of-disclosures; access-review certification campaigns; scheduled restore drills with RTO/RPO evidence; and security-review/risk-analysis gates for PHI, money, authority, or credentials, with no autonomous financial or patient-impacting action enabled by default. Twist: a redacted-log viewer generates signed compliance evidence packs directly from ledger proofs, access decisions, incident approvals, and recovery drills without exporting unnecessary PHI.

## Interfaces and boundaries

Consumes: workforce identity and organization structure, Frappe deployment configuration, adapter-reported key and storage posture, integration identities, and human review decisions. Emits: authorization decisions, integrity-verifiable audit events, privacy and security work items, evidence packs, safeguard exceptions, and recovery attestations to every operational domain. Owns administrative/financial access policy and compliance evidence mechanics; does not own clinical records, declare a deployment HIPAA-compliant, replace counsel or a security risk analysis, inspect openChart databases, or make breach notification decisions autonomously.

## Alternatives and tensions

An external SIEM or immutable-object store can strengthen retention, but making either proprietary service mandatory would violate deployment portability; adapters plus a bench-native floor preserve choice. Field encryption narrows exposure but complicates search, deduplication, and key rotation. Rich audit detail aids investigations yet creates a second PHI surface, so redaction, purpose-limited access, retention policy, and integrity metadata must be designed together. Managed cloud can reduce toil, but every core control, drill, and evidence path must remain operable on premises. ONC security criteria should be tracked where a certifiable module actually falls in scope, never advertised as blanket product certification.

## Open questions

- Which append-only anchor is the portable default: chained database records, periodically signed manifests, external WORM storage adapters, or a layered combination?
- Which role templates cover solo, multi-site, billing-service, and community-health-center operations without turning templates into overbroad grants?
- How should disclosure accounting distinguish treatment, payment, operations, patient-requested, and reportable disclosures while preserving jurisdiction-specific configuration?
- What restore-drill cadence and evidence threshold should block a release or raise a governance exception?

## Relationships

Clustered in [Synthesis: Platform And Trust](openpractice-feature-list-synthesis-platform-and-trust.md). Adjacent: [Operations Tasks And Workforce](openpractice-feature-list-operations-tasks-and-workforce.md), [Interoperability And Exchange](openpractice-feature-list-interoperability-and-exchange.md), [Reporting And Analytics](openpractice-feature-list-reporting-and-analytics.md).
