# Restart review — October 11, 2026

## What stopped the run

The retained failure was a contract/ownership hold, not a recorded quota failure. The first join correction attempt was rejected because denominator reconciliation could still pass when the observed join differed but counts/cardinality matched. Diagnostic recovery authorized the measurement file, but the task's behavioral contract still excluded the measurement consumer. The worker correctly refused to claim the entire assignment was complete. Its named producer packet could not resolve against this task's registered inputs, so the controller held the run.

Evidence: `dag.run.d/reliability/controller.json`, incident `incident_5c0ed2187668`; retained repair source in `dag.run.d/reliability/incident_5c0ed2187668`; rejected salvage commit `0a668cf9c12b58db6c7268b2d445d19b44a8f499`. The retained repair reported 14 scoped and 64 feature tests passing, but those reports are not acceptance of the unresolved consumer behavior.

## Correction

- Explicitly assign `reconcileDenominatorLineageV1` in `src/analytics/measurement.ts` to the corrective join task, together with its real reconciliation regression and valid control. Preserve historical T002/T003 acceptance as historical records.
- Update the execution manifest, feature plan, handoff, task specification, reliability scope and dependent capability descriptions together. Refresh pinned metadata digests without changing product specifications or existing tests.
- Retain rejected source as an unverified candidate; the work adapter exposes same-task retained paths for comparison against current HEAD. It does not automatically restore stale source or accept old test reports.
- Apply the new planner's deterministic validator to all 192 existing task boundaries and declared capability relationships. Report: `docs/build/resume-planning-audit-20261011.json`, valid with no findings. Scope is declared capabilities/topology/file and resource declarations plus existing local boundaries. This is not independent semantic review or proof that every prose prerequisite is represented.
- Use supported stopped amendment and task retry. Compare all 73 completed task objects and controller attempt, semantic/infrastructure counts and deadline with the pre-amendment snapshot. Preserve them. Renew only the expired wall window through supported continuation.

Integration metadata commit: see `.dag-build/resume-before-20261011/applied.json`. Previous run/config/state snapshots are in the same directory. The old incident remains recorded as superseded, with its worktree intact.

## New-engine adoption and limits

The engine was rebuilt from current source before restart. This remains a legacy version-1 reliability run, supported by the updated engine. The new version-2 canonical planner was used for a deterministic audit; it was not silently installed as the active contract. The current migration API requires a fresh contract-managed run for legacy conversion. Therefore version-2 runtime planning holds and canonical acceptance ownership cannot be claimed for this retained run. An explicit migration preserving original requirements and historical evidence is a separate remaining implementation need.

Current model ladder stays Luna xhigh (3 attempts), Sol 6.1 medium (3), Sol 6.1 xhigh (4); no Astra. Concurrency limit is 3; serialized acceptance capacity can admit fewer simultaneous workers.

## Verification and live trial

Build passed. Thirty current planner and dependency regression tests passed with zero failures/skips. Focused controller tests additionally exercise completed-producer repair/publication/reprobe, legacy producer recovery, future-unit exclusion and post-publication input failure. Live success must be measured by newly accepted work and absence of repeated ownership failure; merely spawning a controller is insufficient.

Restart receipt: `.dag-build/restart-20261011-114703/launch.json`, controller PID 20216, started 15:47:03 UTC. At restart the accepted count is 73/192. Fresh acceptance of the consumer correction and sustained downstream progress remain unproven until the live run produces them.
