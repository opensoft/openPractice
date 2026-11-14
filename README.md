# openPractice

openPractice is a Frappe-native, open-source medical practice operations,
scheduling, billing, and revenue-cycle platform.

## Status

This repository is now the assembly root of the openPractice Triad. The
specifications live in the spec leg (`spec/`); the Frappe application
`open_practice`, the planned application/package name, has not been started and
will live in the code leg (`code/`). The practice-management data model has not
yet been implemented.

## Product boundary

openPractice will own administrative and financial workflows, including
registration, scheduling, coverage, authorizations, charge capture, claims,
remittance, patient billing, payments, operational tasks, and practice
reporting.

It will not own signed clinical facts, encounters, medication lists, results,
or orders; those belong to [openChart](https://github.com/opensoft/openChart)
or another connected EHR. MedxFactory may automate openPractice operations
through bounded, capability-aware integration contracts.

## Original implementation

openPractice is an original Frappe application. OpenEMR, Marley/Frappe
Healthcare, other practice-management products, and healthcare standards may
guide requirements and interoperability behavior, but their code is not an
implementation base for this project. openPractice is not an OpenEMR or Marley
fork.

## Spec-driven development

Product and architecture changes use OpenSpec for governance and Spec Kit for
feature specification, planning, implementation, and verification.

- OpenSpec governance lives under `spec/openspec/`.
- Speckit features live under `spec/specs/`.
- The implementation lives under `code/`.
- The pin commit in this assembly root links the two legs, so each
  implementation commit stays traceable to the requirements and tests it
  satisfies.

See [AGENTS.md](AGENTS.md) for the shared workflow and repository constraints,
and [AGENTS-shape.md](AGENTS-shape.md) for the rules of this repository's
shape, including how a leg is advanced.

## Get started

```sh
git clone --recurse-submodules https://github.com/opensoft/openPractice.git
cd openPractice
make bootstrap
```

`make bootstrap` puts each leg on the `main` branch at its pinned commit
where that loses no commit, says why where it does not, and runs the
validators. `make validate` runs the same checks CI does.

## The three legs

| role | repository | path | holds |
|---|---|---|---|
| assembly | `opensoft/openPractice` | `.` | this manifest, the pins, the gate |
| spec | `opensoft/openPractice-spec` | `spec/` | requirements, decisions, acceptance |
| code | `opensoft/openPractice-code` | `code/` | the implementation and its tests |

All three carry the GitHub topic `xf-project-openpractice`, so the
organisation's own search surfaces the group without a checkout.

Adopted in place from [opensoft/openRepoShape](https://github.com/opensoft/openRepoShape)
at `4bf963f0ca416b142652bb608cafe5c76d893faa`. Elected by Brett Heap on
2026-11-14, against `openxFactory docs/project-repo-schema.md`.

## License

Licensed under the [Apache License 2.0](LICENSE).

Never commit real patient information, payer credentials, financial account
data, decrypted records, wallet secrets, or encryption keys to this repository.
