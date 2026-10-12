# Capability and dependency repair — 2026-10-10

## Diagnosis

The run had 73 accepted tasks out of 185. Task `task_30d481ec`
(`003-stage-lineage`) stopped because accepted feature 007 records and
validators carry cardinality/counts but cannot carry declared or observed
join identity and key mappings. The consumer was required to expose this
trace. Retrying that consumer cannot supply a missing producer contract.

The earlier dependency audit registered exported names, which demonstrated
availability but not the behavior the consumer needed. Recovery allowed
ancestor file expansion but still expected the consumer to complete a
cross-boundary repair; a typed ownership failure suspended the run instead
of linking producer work. These are orchestration and contract defects,
not evidence that Luna cannot perform all analytics work.

## Amended graph

The machine-readable [audit](capability-audit-2026-10-10.json) records every
task's prerequisite producer, exact contract, acceptance commands, outputs,
and split/retain decision. It covers 192 units and 278 checklist assignments.
The run keeps its ID and all 73 accepted task records. There are 119 tasks
remaining after one additive producer correction and six splits.

| Existing boundary | Amendment |
| --- | --- |
| Accepted 007 transformation producer | Add `007-join-evidence-correction` as an explicit corrective successor; run its real validator probe and the existing 007 suite. Historical missing fields remain unknown. |
| `003-stage-lineage` | Separate committed source/transformation/join trace from exact admitted measurement/denominator/unit/scope binding. Both jointly complete original T017. |
| `003-prediction-checks` | Separate authoritative comparison/result binding from required integrity checks. Intermediate results cannot claim supported presentation. |
| `016-window-identity-comparison` | Establish immutable identity/version types before pair comparison and orientation. Keep fixtures with their passing production boundary. |
| `005-scope-t003-t004-t005` | Separate two-window computation/comparison from its exact user-facing view. |
| `005-scope-t007-t008-t009` | Separate committed correction/impact traversal from consequential-use reassessment. |
| `006-method-applicability` | Separate customer-unit/dependence/uncertainty handling from population/category applicability. Retain the final ordered fault tests. |

Product spec.md files, shared wire contracts, original assertions, numerical
thresholds, and final feature acceptance obligations are unchanged. Planning
locks and task-text digests are amended explicitly. Original task text and
replacement mapping remain inspectable. No accepted task is relabeled as failed
or deleted, and no attempt counter or wall deadline is reset.

## Producer checks and recovery

Each registered unit provides a named behavioral boundary. Consumers name
the owner and capability, with an executable check. Export checks remain
explicitly weaker supplementary checks. Producer probes execute scoped
behavioral tests, or the feature's actual behavioral suite for a final unit;
zero-test success is rejected. Successful receipts can be reused only for
identical tracked/untracked source, tooling, registered acceptance and the
observed runtime/environment fingerprint. A check that changes those inputs
cannot publish a successful receipt. These are workflow guarantees, not a
security boundary against arbitrary local shell access.

For a registered missing capability:

1. Resolve the exact producer from registered contracts, not a filename guess.
2. Wait for an unfinished producer without retrying the consumer.
3. For an accepted but defective producer, create a linked corrective incident
   under its own file scope and existing bounded model/incident policy.
4. Run its owning checks, accepted shared-file regression checks, and the
   consumer's registered capability probe; obtain independent review.
5. Publish once, then repeat the consumer probe on the integrated source.
6. Resume the consumer only after that probe passes. Retain rejected consumer
   worktrees as unverified evidence; do not copy stale producer edits over the
   accepted correction.

The existing Luna xhigh → Sol 6.1 medium → Sol 6.1 xhigh policy remains. Astra
is not introduced. Ambiguous ownership, unregistered behavior, dependency
cycles, changed authority, unavailable providers, or exhausted budgets stay
explicit holds. Unregistered behavior requires a reviewed graph amendment;
the model cannot silently invent requirements. Transitive suspension is
computed independent of task insertion order.

## Validation and practical limits

- All 25 planning manifests have complete unique checklist ownership.
- The revised 192-node graph is acyclic; each declared producer precedes its consumer.
- 1,461 existing exported producer bindings were checked without missing exports.
- The accepted implementation builds; earlier feature suites passed 460 tests
  with zero failures/skips. Seven completed feature-003 units passed 71 tests
  with zero failures/skips. Logs are retained under
  `.dag-build/capability-repair-20261010/.dag-build/`.
- The join-capability probe fails on the accepted baseline, as expected. The
  newly registered corrective task must make it pass before lineage runs.
- A real controller/CLI regression exercises accepted producer defect → isolated
  repair → deterministic verification → independent review → publication →
  consumer reprobe → successful consumer execution. The consumer uses one
  attempt; the historical producer completion record stays unchanged.

Structural validation does not certify unbuilt future behavior. The controller
must prove each producer's declared behavior before dispatching its consumer.
The new tasks remain pending until their real acceptance and review pass.
Sustained live progress must be measured after restart; these checks do not
establish that the rest of the project will run without any further defect.
