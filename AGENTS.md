Read AGENTS-shape.md first — the rules of this repository's shape.

This is the assembly root of **openPractice** (`openpractice`): the repository an
engineer clones. It holds `project.yaml`, the pins and the gate, and it mounts
the two legs.

| role | repository | path |
|---|---|---|
| assembly | `opensoft/openPractice` | `.` |
| spec | `opensoft/openPractice-spec` | `spec/` |
| code | `opensoft/openPractice-code` | `code/` |

Everything below this line is openPractice's own. The shape wrote the block above
once and does not pin this file, so how this project is built, tested, reviewed
and released belongs here and nothing upstream will overwrite it.

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

## Triad layout

- `spec/` (`opensoft/openPractice-spec`): `ideation/`, `openspec/` (OpenSpec changes and canonical specs), and Speckit feature directories `specs/NNN-*`.
- `code/` (`opensoft/openPractice-code`): the implementation and its tests. The Frappe application `open_practice` has not been created yet.
- Run `make bootstrap` and `make validate` from this assembly root. Feature worktrees live under `worktrees/<NNN-feature>/{spec,code}/` and are never committed.
