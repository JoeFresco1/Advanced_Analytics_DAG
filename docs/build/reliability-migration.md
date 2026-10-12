# Analytics build reliability deployment

Run: `run_b3a47590`. Migration date: 2026-10-07 (Eastern).

## Execution owner

The shared lightweight DAG reliability controller owns this run. Its project
manifest is `docs/build/reliability.json`; the analytics adapter is
`scripts/build/reliability-adapter.mjs`. Do not launch `supervise.mjs`, ordinary
`dag run`, or the former diagnostic supervisor alongside it.

```powershell
node C:/Users/Fresc/lightweight-dag/dist/cli.js reliable status --config docs/build/reliability.json --json
node C:/Users/Fresc/lightweight-dag/dist/cli.js reliable resume --config docs/build/reliability.json --json
```

`resume` is used after a stopped/crashed controller. A live verified lease rejects
a second controller. `.dag-build/reliable-launch.json` identifies the current
background process and its log paths. The viewer remains at
`http://localhost:8792/r/run_b3a47590`.

## Preserved authority and budgets

- All 164 units are registered. The 65 completed tasks were retained.
- Semantic rejection counts were imported from legacy events, with only the
  already recorded historical exclusions. Migration does not reset a task's
  model ladder. The failing acceptance task had three counted rejections.
- Worker policy: Luna xhigh, 3 attempts; Sol 6.1 medium, 3 attempts; Sol 6.1
  xhigh, 4 attempts. No Astra or Terra. Diagnostics use a separate fresh Luna
  xhigh invocation. Analytically pinned fixture workers keep their own model.
- The authorized restart creates one 24-hour session deadline; amendments and
  resumes retain that deadline. Individual stages have explicit limits. The
  engine cycle has a two-hour outer limit so a healthy full acceptance and
  review cycle is not killed at the former generic fifteen-minute boundary.
- Maximum scheduling concurrency is three. The first rollout reserves a single
  acceptance resource per unit, so admissions are deliberately serialized while
  the controller is proven on live analytics tasks.

## Project boundary

The adapter retains the frozen contract lock, task-text digest, checklist
assignment checks, original acceptance argument vectors, final feature guard and
unit verification report requirement. The controller also protects existing test
module imports, helpers and assertions. New top-level tests may use dynamic
imports; existing acceptance cannot be weakened.

During a recorded recovery, only the consumer's registered files and explicitly
authorized registered ancestor implementation files are writable. The same
scope is used when validating affected producers, but only the consumer's
assigned checklist markers may change. Ordinary tasks have no such expansion.

The optional user MCP configuration is not loaded for repository-only workers,
avoiding the observed transport failures without changing user configuration or
authentication. Windows workers use the working automatic approval harness;
diagnosis and review are instructed to be read-only and review mutation is
checked independently. This is a workflow boundary, not an OS isolation claim.
Python verification uses a cache inside the ignored per-worktree runtime folder.

## Incident being repaired

The T015 acceptance task invokes `demo-evidence --scenario matrix`. The accepted
upstream producer exposed the parser and scenario APIs but omitted the executable
CLI entrypoint. A saved attempt added that implementation; the old report-only
task guard rejected the producer edit as unowned. The subsequent old diagnostic
failed, leaving the run stopped.

The new controller's fresh diagnostic confirmed the ownership gap and authorized
only `src/analytics/demo-evidence.ts` as an ancestor implementation expansion.
Sol 6.1 medium is repairing it in an isolated worktree. Publication requires the
unchanged consumer and affected producer checks, matrix probe and independent
structured review. The controller then uses the supported retry operation and
requires a fresh accepted task completion before marking the incident resolved.

## Rollout evidence and limits

The shared controller's 25 lifecycle/contract tests passed, including real CLI
repair/publication/requeue, process restart, provider-budget preservation,
registered producer recovery, protected tests, long-stage validation and exclusion
of unfinished units that merely share an acceptance file. The existing rejected
work salvage regression also passed. Shared deployment fixes are committed in
lightweight DAG as `48639e1`.

Live deployment has reached diagnostic confirmation, preparation and a real
authorized repair worker. This document does not certify ten successful live
units or claim that the current acceptance task has completed. Durable incident
and acceptance receipts are the authority for subsequent progress.

## Post-publication deployment correction (2026-10-08)

The repair passed all registered consumer/producer checks and independent review
and was published as `8dc97628f1e8cbc692341036903d9b3848d8c045`.
The subsequent integration probe failed because Git publication transfers source,
while ignored `dist/` outputs and dependencies remained in the repair worktree.
The integration checkout had no compiled demo executable.

The adapter now prepares and builds the integration checkout before its matrix
probe. The shared controller also preserves an accepted publication receipt and
opens a bounded fresh incident when a post-publication input probe fails, rather
than repeatedly treating an already requeued incident as active recovery. Its
iteration cap has an explicit exhausted status. A real controller regression
checks that a broken post-publication probe runs once, retains the published
receipt, and stops at an unresolved contract instead of polling indefinitely.
Resume retains the original session deadline, rejection counts and model policy.
