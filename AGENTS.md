# Agent Instructions

<!-- OPENSPEC-SPECKIT-GLOBAL:START -->
## Shared OpenSpec/Speckit Protocol

This repo uses the user-global OpenSpec/Speckit workflow instead of duplicating
process rules in every repository.

- Global agent entrypoint: `$HOME/.agents/AGENTS.md`
- Workflow protocol: `$HOME/.agents/protocols/openspec-speckit-workflow.md`
- Bootstrap contract: `$HOME/.agents/protocols/project-agent-bootstrap.md`

Repo-local sections below remain authoritative for project-specific commands,
runtime prerequisites, source-of-truth docs, tests, and deployment constraints.
<!-- OPENSPEC-SPECKIT-GLOBAL:END -->

## Repository Role

openPractice is the public Frappe-native practice-operations foundation. It
must remain independently usable with openChart or another EHR and must not copy
or fork OpenEMR or Marley implementation code.

## Safety and Data

- Never commit real PHI, payer credentials, financial account data, decrypted
  records, wallet secrets, or encryption keys.
- Use synthetic or explicitly de-identified fixtures for development and tests.
- Preserve authorization, financial authority, reversibility, provenance, and
  audit behavior in operational designs.
- Do not enable autonomous financial or patient-impacting actions by default.

## Current Runtime Status

The repository is at governance-scaffold stage. There are no Frappe runtime,
build, migration, or test commands yet. Add those commands here when the first
governed implementation establishes them.
