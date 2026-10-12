# Neutral Execution Contract

**Package:** `@dag-orchestrator/neutral-execution`  
**Source owner:** feature 001 at `packages/neutral-execution/`  
**Shared contract:** `docs/build/interfaces.md`

## Public API

Export `NeutralTaskV1`, `NeutralRunOptionsV1`, `NeutralExecutionV1`,
`runTasksV1`, `readExecutionsV1`, `stopRunV1`, and `recoverRunV1`. Tasks use an
executable plus argument vector, never shell interpolation, and declare task
ID, dependency IDs, working directory, allowed output root, timeout, silence
limit, and environment overrides. Execution records include task/attempt IDs,
times, process status/failure kind, exit code, invocation digest, stdout and
stderr log references, and produced paths.

The neutral package owns dependency order, process execution, logs, timeouts,
locks, artifacts, stop/cancellation, and interrupted/crash recovery. It does
not assign analytics meaning to process status. Preserve the frozen neutral
compatibility vector suite for dependency order, argv, exit status, logs,
timeout, stop, locks, working/artifact roots, recovery, and replay.

## Analytics invocation

`src/analytics/runner-adapter.ts` is the only analytics caller of
`runTasksV1`. It supplies retries `0`, `maxAttempts: 1`, review rounds `0`,
repair rounds `0`, an empty fallback chain, and final review `off`; the
adapter rejects contradictory overrides before process start. An automated
failed-check, null/negative result, or runner-success/check-failure fixture
must produce no retry, review redo, repair, or fallback event.

If a controller later admits an infrastructure re-execution, it records a
separate execution ID under the same analytical attempt, after classification
and budget admission. This neutral package does not make that decision.

## Compatibility distribution

The feature creates a local workspace package and a tested legacy adapter.
Both use identical conformance vectors. Build a local package archive and
write its actual SHA-256 digest under `artifacts/neutral-execution/`. Remote
publishing is not part of acceptance. Do not edit the original product
checkout during feature 001.
