# Tasks before implementation

This supersedes the original workflow that generated plans/tasks inside each
feature's implementation node. Generate all implementation task lists first,
validate their coverage and dependencies, then compile and run their DAG.
Workers, planners and agent reviewers use `gpt-6-luna` with `xhigh` reasoning.
Accepted product specifications and checks remain read-only.

## Planning outputs

For each of the 23 feature specs, use the local `speckit-plan` then
`speckit-tasks` skills. Generate `plan.md`, `research.md`, `data-model.md`,
`contracts/`, `quickstart.md`, `tasks.md` and `execution.json` inside the feature
directory. Do not implement code or mark implementation tasks completed.
Use the shared architecture/interface documents as existing design input.
The fixture-bootstrap and final convergence nodes have equivalent artifacts
under `docs/build/task-plans/<key>/`; they are not new feature specifications.

Each `tasks.md` has unique `T001`-style checklist IDs, phases/user stories,
exact file paths, dependencies, independent verification and requirement
coverage. Numbered proposal cases (`T01`..`T61`) are distinct from feature-local
implementation task IDs. Do not conflate them.

## Execution manifest version 1

`execution.json` has exactly these top-level fields:

```json
{
  "schemaVersion": 1,
  "featureKey": "007",
  "requirementTasks": {"FR-001": ["T001"]},
  "successTasks": {"SC-001": ["T002"]},
  "edgeCaseTasks": {"EC-001": ["T003"]},
  "units": [
    {
      "id": "foundation",
      "title": "Implement the source identity boundary",
      "taskIds": ["T001", "T002", "T003"],
      "dependsOn": [],
      "files": ["src/analytics/data.ts", "src/analytics/acceptance/007.test.ts"],
      "instructions": "Implement these specified tasks and their production callers.",
      "acceptance": [{"program": "pnpm", "args": ["build"]}]
    }
  ]
}
```

The sample is structural only; replace it with the complete real task list.
Every checklist task belongs to exactly one unit. Every FR and SC maps to
at least one real task. `EC-001` onwards map in order to the spec's Edge Cases
bullets and cover all of them. Nonfeature plans have empty requirement,
success and edge maps. Each unit needs a nonempty file list, imperative scope
and runnable acceptance commands. Paths stay inside the repository.

Units are bounded work with one coherent deliverable and an observable
check. Bundle closely related checklist steps that change the same module
or need each other to compile. Prefer independently testable story/phase
units; do not force a separate worker for each trivial checkbox. Do not
collapse a whole multi-story feature into one unit. Use at least two units
for every feature, including its final acceptance/verification unit.

`dependsOn` names local unit IDs. Cross-feature prerequisites come from the
existing feature graph and are added by the compiler. Units within a feature
are also serialized in listed order to protect shared files and its checklist.
Independent features run concurrently in isolated worktrees. The final unit
per feature runs all of its acceptance checks and writes `verification.md`,
with every FR/SC mapped to actual evidence. Plans must assign creation of all
code outputs listed for that feature in `docs/build/graph.json`.

Commands use explicit `program` and `args` arrays, expected exit 0. Do not
embed shell expressions, pipes, destructive operations, secrets or natural
language inside executable command text. Use script files for complex checks.
Choose checks that can pass after this unit and its prerequisites; do not
demand later units' implementations. Intermediate units can run targeted
tests; final feature acceptance rejects skipped/TODO cases.

## Phase gate and handover

Planning workers may write only their own planning artifacts. A deterministic
guard checks unchanged accepted contracts, task/coverage completeness, unit
ownership, command safety, dependencies and absence of product-code changes.
A fresh Luna reviewer checks the actual plans against their spec, shared
contracts and current source. Planning failures stop the handover.

Only after all plans pass does the coordinator copy the accepted planning
artifacts from the planning integration branch, compile the implementation
graph, and start that graph. Implementation workers consume the existing
task list, complete only their assigned IDs and retain its numbering/scope.
Every checklist ID maps to one executable unit in the handover manifest.
No architecture-regeneration node precedes implementation.

## Authorized scope amendment — 2026-10-04

The user authorized finer decomposition and an audit of subsequent tasks. The amended execution manifests own the bounded work order and intermediate acceptance commands; descriptions of former unit grouping or whole-suite timing in older task prose are superseded by those commands. Test-first setup stays with the implementation needed to pass its assigned assertions. Every intermediate unit adds actual behavioral tests with the exact scope:<unit-key>: prefix; check-scope.mjs rejects a filter with no passing tests. Final feature checks still run the complete accepted feature and repository acceptance; no analytical requirement or threshold is reduced.

The scope audit records every original unit and every expanded checkbox obligation. Already completed units retain identity and historical evidence. The previous evaluator output is retained as an explicitly unverified repair baseline; its presence does not approve its unassigned boundaries. Old recovery files cannot overwrite post-amendment repairs. The quota ladder remains Luna xhigh (two retries), Sol 6.1 medium (two retries), Sol 6.1 xhigh (three retries), with separate same-tier reviewers and no automatic Astra.

Validation uses the accepted specification versions on dag/run_b3a47590. Newer working-folder specification drafts are preserved separately and are not silently incorporated into this frozen run.
