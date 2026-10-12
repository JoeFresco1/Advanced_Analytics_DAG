# Advanced analytics implementation run

This directory controls the product build. Runtime analytical inquiries have
their own evidence policy; code-build retries and reviews are not analytical
retries or approvals.

## Tasks before implementation

The original 26-node run generated tasks inside each feature worker. It is
superseded by the task-first workflow in [task-generation-contract.md](task-generation-contract.md).
First generate/review all Spec Kit plans and task lists. Then compile every
checklist task into an executable unit, with [task-to-dag.md](task-to-dag.md)
recording the mapping, and start implementation. No architecture-regeneration
node precedes that implementation run.

`node scripts/build/start-taskflow.mjs` launches the two phases. The current
phase and any handover failure are recorded in `.dag-build/taskflow.json`;
logs are `.dag-build/taskflow.out.log` and `.dag-build/taskflow.err.log`.
`docs/build/run-manifest.json` identifies the active planning or implementation
run. Planning failures prevent implementation from starting.

## Execution

The local DAG orchestrator runs the compiled implementation units using Codex CLI
with `gpt-6-luna` and `model_reasoning_effort="xhigh"` for every planning,
implementation, and agent-review stage. Concurrency is three. Each task uses
an isolated Git worktree and merges accepted work into the run's integration
branch. The original checkout remains available for inspecting the build.

The implementation graph consumes the existing shared architecture, starts
with the product boundary, then
freezes reference fixtures before evaluating analytical behavior. Focused
features feed eight integrated user paths. Final nodes evaluate the complete
product and verify the merged result. See [graph.json](graph.json) for module
ownership and dependency details and [run-manifest.json](run-manifest.json)
for task IDs.

Every feature's Spec Kit plan and task list exists before implementation starts.
A deterministic reviewer executes required tests and checks accepted-contract
hashes from the controller checkout. A fresh Luna reviewer checks actual
behavior and production consumers. Missing verdicts, changed acceptance
contracts, unfinished tasks, and skipped acceptance cases fail closed. One
review correction and one process retry are allowed for code-building work.

Limits are two hours per command, ten minutes without output, and a 24-hour
scheduling cutoff. The cutoff does not guarantee termination of work already
running. Failures leave their dependent nodes blocked with logs retained.

## Inspect and recover

Run these from the repository root with the existing global `dag` CLI:

```powershell
dag status --json
dag layers
dag logs --id <task-id>
dag retry --id <task-id> --cascade
dag run --worktree task --concurrency 3
```

Do not edit a locked run. Do not lower a reference threshold to recover a
failed implementation. The final handover is `COMPLETION.md`, with executable
user instructions in `docs/QUICKSTART.md`. A running or green individual node
does not mean the whole product is complete.
