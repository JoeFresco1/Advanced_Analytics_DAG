# Research Decisions: Integrated Delegated Inquiry

## Decision 1: Compose the already-owned analytics capabilities

**Decision**: The 003 controller is an orchestrator over the frozen 007, 008, 009, 010, 011, 012, and 013 facades. It calls the single-stage executor only after admission, and waits for each stage to settle before admitting a worker-authored continuation.

**Rationale**: The build architecture assigns data identity, plan/exposure, stage publication/recovery, required checks, viewer traces, bounded inquiry admission, and predictive computation to those features. Keeping 003 as the production caller proves the complete question path without a duplicate runner, calculator, policy, or viewer.

**Alternatives considered**: A second analytics scheduler or TypeScript predictive implementation would duplicate owners and weaken exact execution/result binding; both are excluded by the frozen architecture.

## Decision 2: Use one fixed, typed extension registry

**Decision**: Define AnalyticsExtensionV1, typed command registration, capability reporting, provider loading, dependency ordering, and lifecycle dispatch in src/analytics/extensions.ts. Keep an explicit module/export allowlist from docs/build/interfaces.md; never load a module name supplied by a fixture, user, worker, or command.

**Rationale**: The shared interface freeze gives later feature owners a stable seam while keeping shared CLI/controller files under node 003. Deterministic allowlisting prevents data-controlled code loading and makes incremental capability availability inspectable.

**Alternatives considered**: Filesystem plugin discovery or arbitrary import paths would create an unowned trust boundary. A mutable registration file would duplicate the frozen graph/interface authority.

## Decision 3: Order providers and reject invalid registries before dispatch

**Decision**: Validate unique extension IDs and command token paths, dependency presence, command argument schemas, and acyclicity at startup. Produce a deterministic topological hook order. Skip only a missing exact optional module and expose its unavailable commands as such. Propagate import/runtime failures, provider export/schema faults, missing dependencies, duplicate commands, cycles, and required-hook failures.

**Rationale**: The interface contract distinguishes an expected absent provider during incremental development from a broken provider that exists. Stable ordering makes orientation, attempt, execution, evidence, and finalization effects replayable and reviewable.

**Alternatives considered**: Catching all import failures as optional absence could hide broken transitive imports. Registration-order execution would make provider order unstable and could run answers before ledger assessment.

## Decision 4: Keep worker authorship separate from authoritative computation

**Decision**: Pass the worker a controller-created context packet containing the original question, accessible source and policy refs, prior plans/results/exposure, current budget, capability manifest, and exact requested outputs. Accept typed proposals and authored code/check artifacts. Mechanically validate identity, schema, access, and budget, then use fixed 007/013 Python calculators and 010 validators through 009.

**Rationale**: The worker performs analytical planning and explanation; controller admission and authoritative computation establish which artifacts and values are reportable. An opaque worker calculation may not support a numerical assertion.

**Alternatives considered**: Treating a worker transcript or its own numeric output as authoritative would violate FR-004, SC-008, and the evidence-binding contract. Having TypeScript recompute statistics would duplicate feature 013.

## Decision 5: Preserve history across revision and harness restart

**Decision**: Every relevant observation/exposure and substantive plan revision is recorded. Replacement workers receive prior plan/result/exposure refs and remaining allowance. Code or policy revision changes invalidate checks for affected subjects; code-only repair creates a new execution, and post-observation method changes remain outcome-informed.

**Rationale**: Plan interpretation is historical state owned by 008. Restart, approval, or a new invocation cannot remove an earlier exposure.

**Alternatives considered**: Treating a new worker invocation as an independent inquiry would erase exposure and budget. Reusing successful checks against changed subjects would violate the check-binding invariant.

## Decision 6: Finalize through stable-ID replay, not a cross-scope transaction

**Decision**: The controller writes run terminal intent, commits required project-memory records that reference the committed run revision, then appends a run completion acknowledgement referencing the committed project revision. Derive stable commit/event IDs from inquiry, finalization ID, and scope; replay the same request after interruption. Return completed only after all required memory hooks acknowledge durability.

**Rationale**: The frozen 009 contract has separate run and project manifests and promises no cross-scope atomic transaction. Ordered, idempotent writes provide recoverability without claiming atomicity.

**Alternatives considered**: A single database transaction across run/project scopes is unavailable in the shared persistence API. Returning before the last acknowledgement can advertise work as complete when a required answer/ledger write is missing.

## Decision 7: Use the canonical bounded inquiry and predictive states

**Decision**: Consume the feature 012 budget and outcome resolver; use the six canonical inquiry outcomes and the 013 predictive comparison states. Reserve required checks and final reporting before optional work. Do not retry a valid null/non-improvement result to seek a favorable comparison.

**Rationale**: Exit status expresses process execution only. The canonical outcome and frozen delta interval classification preserve analytical meaning.

**Alternatives considered**: Mapping every nonzero exit to an inquiry failure, or every zero exit to answered, collapses execution and evidence and is explicitly prohibited.

## Decision 8: Prove the first-screen interpretation and the review gaps explicitly

**Decision**: Add full-path signal, null, and delta-boundary predictive fixtures; verify first-screen wording separates predictive improvement, causal influence, and non-improvement; assert a value present only in opaque worker output appears zero times as a supported report value.

**Rationale**: SC-001, SC-006, and SC-008 require these observable outcomes. The boundary fixture exercises the exact inclusive/exclusive comparisons in 013 FR-008 rather than a nearby value.

**Alternatives considered**: Relying only on 013 unit tests would not prove those cases reach the integrated controller and browser answer. A general prose review would not establish a zero count for opaque values.

## Decision 9: Test the owned path with the existing toolchain

**Decision**: Use pnpm build and pnpm test with the Node built-in test runner. Inject local worker, provider, stage, check, viewer, and persistence adapters for deterministic coverage; record live gpt-6-luna/xhigh settings only in the live demo path.

**Rationale**: package.json already supplies these scripts and the build contract asks for unit acceptance that can pass after its prerequisites. The feature adds no dependencies or manifest changes.

**Alternatives considered**: Adding a second test framework or a paid runtime is not necessary and would change shared tooling ownership.

## Remaining Clarifications

None. Production behavior, ownership, state values, fixture scope, interface seams, and thresholds are specified by the accepted feature spec and frozen build documents.
