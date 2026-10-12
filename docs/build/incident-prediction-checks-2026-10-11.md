# Prediction-checks recovery investigation — 2026-10-11

Run: `run_b3a47590`. Task: `task_0b7a3bae` (`003-prediction-checks`). Incident: `incident_8c330d62ba99`. Investigation only; no task status, retry counters, acceptance requirements, or controller policy changed.

## Confirmed failure chain

1. The original engine attempt timed out after 900 seconds. Its exact underlying cause remains unproven; the diagnosis cited worktree and MCP errors as environment evidence.
2. Recovery launched ten repair commands: Luna xhigh three, Sol 6.1 medium three, Sol 6.1 xhigh four. All ten returned code 0 and were subsequently rejected by the same deterministic check: `Assigned task incomplete: T018`. Repair command time totaled approximately 56.7 minutes.
3. The canonical unit assigns only the comparison portion of T018. T028 (`003-prediction-integrity-checks`, task `task_df897b0b`) owns the remaining integrity checks and depends on the comparison unit.
4. Preserved T018 checklist text still includes both comparison and integrity checks. The tasks document explicitly says split units jointly satisfy original obligations.
5. `scripts/build/reliability-adapter.mjs:84` requires every original assigned checkbox to be complete before running local acceptance. This makes the comparison unit require certification of future dependent work.
6. Workers explicitly emitted `DAG_PLANNING_FINDINGS` about this contradiction, beginning with the first repair. Some earlier packets have inconsistent field names; the last packet uses canonical `references` and `evidence` fields. These reports still did not reach planning recovery.
7. The adapter prints free-text work/repair output and treats only `DAG_FAILURE` and `DAG_SCOPE_REQUEST` as nonzero exits. The controller repair path consumes failure/scope packets, but not standalone planning findings from successful repair output. The later checkbox check emits an implementation failure without planning findings. Thus repeated planning complaints are charged as semantic implementation rejections.
8. Ten semantic rejections exhausted the permitted model ladder. Suspension prevented an unauthorized Astra escalation.

## Independently reproduced

From the retained incident worktree, the current adapter `check task_0b7a3bae` exits 1 at the T018 checkbox before executing local acceptance.

From the same worktree, `node scripts/build/check-scope.mjs 003-prediction-checks src/analytics/acceptance/003.test.ts` exits 0: 12 tests pass, zero fail, zero skip. This verifies the scoped tests, not complete product acceptance or independent review. Retained implementation remains unaccepted.

## Required corrective boundaries

- Give decomposed local units independent completion receipts; keep aggregate original checklists open until all contributors are verified. Do not falsely check T018 or remove its integrity obligations.
- Consume and validate grounded planning findings at the repair output boundary, including exit-zero output, before charging another implementation attempt. Normalize packet handling between ordinary engine workers and reliability repairs.
- Detect the repeated same gate failure with no relevant acceptance progress and route to bounded planning correction rather than consuming the entire model ladder.
- Audit other split original checklist obligations for this same local-versus-aggregate gate contradiction.
- Verify positive and negative composed cases: a local comparison can pass while aggregate T018 is open; missing local behavior still fails; aggregate T018 stays incomplete until integrity checks pass; grounded repeated planning reports hold recovery without exhausting implementation budget.
- Preserve the existing exhausted-budget evidence. Any subsequent correction/continuation must use supported amendment and recovery controls; do not silently reset counters.

## Evidence locations

- `dag.run.d/reliability/controller.json`: attempt profiles, times, semantic count 10.
- `dag.run.d/reliability/events.jsonl`: ten identical check rejections and final suspension.
- `dag.run.d/reliability/incident_8c330d62ba99.json`: original diagnosis and latest failure.
- Incident worktree `.dag-build/reliability-adapter/task_0b7a3bae-repair-*.json`: worker final reports including planning findings.
- `docs/build/execution-graph.json`: comparison-only assignment and separate integrity unit.
- Incident worktree `specs/003-delegated-inquiry/tasks.md`: T018, T028 and split-obligation amendment.

