# Reimbursement and Retention Control Overview — Brainstorm

Status: brainstorm
Kind: reference
Summary: openPractice can pair wallet-backed operational authorization with versioned reimbursement-retention and legal-hold policy while keeping sensitive billing data off public chains.
Topics: reimbursement-retention-control, openpractice, billing, reimbursement, medicaid, legal-retention
Repository context: openPractice portable practice-operations behavior and MedxPractice specialization seam
Captured: 2026-08-29

## Possible feats

- **Governed reimbursement record lifecycle** — trace each protected operation
  from authority through final legally cleared disposition.

## Motivation

Practice and billing systems hold sensitive data whose value and obligations
outlive a single access session. Medicaid and other reimbursement records may
need preservation through claims, appeals, audits, investigations, and legal
holds even after a patient, employee, vendor, or AI service loses access.

## Goals

- Enforce purpose- and role-bound authority for administrative and financial
  operations.
- Audit allowed and refused attempts without publishing claim metadata.
- Represent retention obligations as versioned, sourced policy.
- Keep legal holds separate from access grants and revocation.
- Refuse disposition until every applicable obligation is cleared.
- Witness opaque lifecycle evidence through Kaspa and daily Bitcoin batches.

## Non-goals

- Defining signed clinical facts owned by openChart.
- Hard-coding one Medicaid or HIPAA retention duration.
- Treating patient revocation as authority to destroy legally retained records.
- Claiming an anchor proves proper reimbursement, retention, or deletion.
- Publishing payer, claim, patient, or exact retention metadata publicly.

## What the system delivers

openPractice can produce an attributable chain from billing authority and use
through correction, appeal, hold, retention, and authorized disposition. Each
step remains owner-local and may receive an opaque external witness.

## System model

```text
WALLET + ORGANIZATION + PAYER POLICY
                  |
          OPENPRACTICE USE GATE
                  |
       claim / billing / remittance
                  |
     retention obligations + holds
                  |
          disposition decision
                  |
       storage and key lifecycle
                  |
   KASPA NOW + DAILY BITCOIN PROOF
```

## Cluster map

- [Synthesis: Reimbursement Evidence Lifecycle](reimbursement-retention-control-synthesis-evidence-lifecycle.md)
  — relates operational authority, use events, changing obligations, legal
  holds, disposition, and external evidence.

## How it fits

openPractice owns portable billing and practice-operation semantics.
MedxPractice can bind MedxWallet profiles, MedxFactory policy, specific payer
programs, jurisdictional retention sources, and deployment configuration
without making the public product depend directly on Medx.

## Key decisions and open questions

The first governed implementation needs a narrow payer/program and
jurisdiction scope, legal review, authority matrix, obligation sources, hold
roles, backup behavior, and evidence presentation policy. Those facts should be
profiles, not inferred from generic HIPAA labels.

## Document map

### Synthesis documents

- [Reimbursement Evidence Lifecycle](reimbursement-retention-control-synthesis-evidence-lifecycle.md)

### Atomic documents

- [Billing and Reimbursement Use Control](reimbursement-retention-control-billing-use.md)
- [Retention, Legal Hold, and Disposition](reimbursement-retention-control-legal-retention.md)
