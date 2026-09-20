# Capability: kernel-self-rewrite

One line: the governance loop can rewrite its own kernel documentation
(AGENTS.md / README / docs/) through the identical machine-gated closed loop
used for any change — no human review, no privilege.

## Contract
- Trigger: a work order (mandates repo) declaring a doc-vs-reality drift.
- Loop: episode (gateway `POST /v1/workorders`) -> branch -> PR -> machine
  gates (test / opa / guard / eval-smoke) -> sign.yml evidence -> evidence
  PR -> squash merge -> VERIFYING -> (owner spot check) ->
  CLOSED.candidate_ready.
- Doc-sync gates: `tests/test_docs_sync.py` keeps AGENTS.md/README/docs
  equal to the tree (directory-map paths exist, documented routes ==
  gateway/app.py routes, work-order numbers present, stale-reference scan).
- Discipline: manual 7.2/7.4 — unknowns go to ruling, never administratively
  closed; any urge to have a human write code, pass messages or approve on
  the system's behalf is itself recorded as a defect work order.

## Implementation
Kernel repo (monorepo): AGENTS.md, README.md, docs/, tests/test_docs_sync.py.
Delivered by WO-105 (kernel PR#28). Eval: eval-gate repo
`suites/wo105-kernel-self-rewrite.md` (G1 closed-loop verification, G2 doc
sync, G3 owner spot check).

## Operation
No runtime surface — documentation + doc-sync tests only. The capability is
the process: every future doc drift is fixed through this same loop.

## Evidence
`evidence/` — signed by sign.yml (evaluator=eval-signer) per manual §6.3.
