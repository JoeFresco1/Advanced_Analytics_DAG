# Final convergence research decisions

This plan continues the recovered node-99 Spec Kit draft. It resolves audit,
evidence, and handover details from the accepted product contract and frozen
build interfaces; it does not change product behavior or acceptance policy.

## Decisions

| Topic | Decision | Rationale |
| --- | --- | --- |
| Feature report inventory | Expect one `specs/<feature>/verification.md` for each of the 23 feature specs. Inventory missing files and preserve missing/planned outcomes as incomplete. | The acceptance plan requires each feature owner to report actual FR/SC/edge/T outcomes. A planned report is not evidence that its acceptance ran. |
| Exact coverage denominator | Derive 234 FRs, 219 SCs, 115 ordered edge cases, 61 primary T owners, and eight integrated path IDs from the accepted specs, maps, and registers. | The FR map alone does not enumerate every SC. Counts must be computed from their authoritative sources, not copied from a planned matrix. |
| Completion evidence shape | Add a machine-checkable matrix to `docs/build/COMPLETION.md` with one row per expected FR, SC, edge, T case, and integration path, plus a separate 23-row verification-report inventory. Every status has an evidence reference. | The final test can compare exact keys, owners, duplicates, missing rows, extra rows, and evidence links without interpreting a narrative completeness claim. |
| Evidence quality | Count only observed test/command/evaluator records, actual IDs, or inspected browser evidence as acceptance proof. Missing, pending, TODO, or planned material is not a pass. Record an unavailable/not-run result as such, with its actual reason/evidence. | Distinguishes current evidence from task plans while retaining failures and honest limits. |
| Report outcome labels | Use completion-only reporting labels for whether an acceptance criterion passed, failed, was blocked, not run, or unavailable. These labels do not replace inquiry, Claim, Check, exposure, validity, reproduction, or authorization vocabularies. | The canonical analytics state vocabulary remains the only authority for analytics states. A test proving an inconclusive or negative analytical result can itself pass. |
| Integrated paths | Use the eight exact IDs listed by `specs/README.md` and the acceptance plan: 001, 002, 003, 020, 004, 022, 005, and 006. | These are the accepted integration milestones; command actions and browser meanings come from `docs/build/interfaces.md`. |
| Evaluator and live model | Use feature 018's actual evaluator API after the response and the frozen `gpt-6-luna` / `xhigh` CLI profile. Keep evaluator keys, labels, and planted-fault explanations outside worker context. | The interface document specifies evaluator separation and does not define a standalone evaluator CLI. |
| Ownership of repairs | Node 99 fixes only its acceptance and handover outputs. An upstream production gap is recorded against its existing owning feature/task and fails convergence until a separately assigned bounded repair and affected rechecks are complete. | The graph and interface contracts assign exact file ownership; a convergence task must not become an unowned feature implementation. |
| Access and cost claims | Use synthetic/local fixtures. Label local shell access as workflow-withheld where it can bypass product surfaces. Report cost only when the selected harness exposes it; otherwise mark unavailable. | Matches the access contract and avoids claiming guarantees or telemetry the environment does not provide. |

## Snapshot observed during plan repair

The checkout contains 23 feature specs and the accepted audit sources report
234 FRs and 115 edge cases. A direct scan finds 219 SC definitions. Nine
feature verification reports currently exist: 005, 006, 008, 013, 014, 018,
020, 021, and 022. Their current contents are planning evidence marked
pending; the other fourteen report files are absent in this planning snapshot.
This baseline is not a forecast of the merged implementation. T001 must
inventory the merged checkout again and may report success only from actual
feature evidence.

## Alternatives rejected

- Treating the presence of a `verification.md` or a structural audit pass as
  behavioral acceptance. Neither proves that the relevant tests ran.
- Checking FR-to-SC references only. That would miss SCs that have no unique
  primary FR mapping, as well as edge order and T-case ownership.
- Letting T006 patch cross-feature production files as “small” integration
  fixes. File ownership remains with the feature graph; upstream gaps fail
  convergence and need a bounded repair with that owner.
- Guessing a browser URL or evaluator command. Use the route and invocation
  actually emitted by the implemented interfaces and feature 018.
- Claiming a frozen fixture pass demonstrates broad reliability or enforced
  holdout isolation. State only the measured profile and real access boundary.

## Frozen inputs consulted

- `docs/specs/advanced-analytics-dag-proposal.md` revision 2.6 and
  `.specify/memory/constitution.md` version 1.4.1.
- `docs/specs/state-vocabularies.md` and
  `docs/specs/access-and-judgment-contract.md`.
- The 23 accepted feature specs, `specs/README.md`, both verification maps,
  and `docs/specs/traceability-audit.md`.
- `docs/build/ARCHITECTURE.md`, `interfaces.md`, `acceptance-plan.md`,
  `graph.json`, and `task-generation-contract.md`.
- Existing feature-018 evaluator contract and its frozen verification inputs.

No product command or implementation test is run during this planning stage.
