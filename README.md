# openPractice

openPractice is a Frappe-native, open-source medical practice operations,
scheduling, billing, and revenue-cycle platform.

## Status

The repository currently contains project-governance and specification tooling.
The Frappe application and practice-management data model have not yet been
implemented.

The planned Frappe application/package name is `open_practice`.

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
feature specification, planning, implementation, and verification. Specs and
code live together in this repository so each implementation commit remains
traceable to its requirements and tests.

See [AGENTS.md](AGENTS.md) for the shared workflow and repository constraints.

## License

Licensed under the [Apache License 2.0](LICENSE).

Never commit real patient information, payer credentials, financial account
data, decrypted records, wallet secrets, or encryption keys to this repository.
