Read AGENTS-shape.md first — the rules of this repository's shape.

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

## Triad layout

- `spec/` (`opensoft/openPractice-spec`): `ideation/`, `openspec/` (OpenSpec changes and canonical specs), and Speckit feature directories `specs/NNN-*`.
- `code/` (`opensoft/openPractice-code`): the implementation and its tests. The Frappe application `open_practice` has not been created yet.
- Run `make bootstrap` and `make validate` from this assembly root. Feature worktrees live under `worktrees/<NNN-feature>/{spec,code}/` and are never committed.
