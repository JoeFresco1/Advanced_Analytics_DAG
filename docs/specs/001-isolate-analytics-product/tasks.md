# Tasks: Isolate the Analytics Product

**Input**: Design documents in this directory (`plan.md`, `research.md`,
`data-model.md`, `contracts/`, and `quickstart.md`) plus the accepted
`spec.md`.  
**Prerequisite**: Build node `00` and the frozen interface/architecture
contracts.  
**Organization**: Tasks are grouped by setup/foundation, then the two feature
stories in priority order. Every task is unchecked; implementation has not
started.

## Phase 1: Setup

**Purpose**: Establish the package workspaces and locked Python worker project.

- [ ] T001 [P] Configure the pnpm workspace, neutral package metadata, package build/test/local-pack scripts, and the Ajv 2020-12 runtime dependency used to validate generated Pydantic schemas in TypeScript; update the root workspace dependency and lockfile in `pnpm-workspace.yaml`, `package.json`, `pnpm-lock.yaml`, `packages/neutral-execution/package.json`, and `packages/neutral-execution/tsconfig.json`.
- [ ] T002 [P] Bootstrap the uv Python 3.11+ project, strict runtime/test dependencies, lock, and package export in `python/pyproject.toml`, `python/uv.lock`, and `python/analytics_worker/__init__.py`.

## Phase 2: Foundational Contracts

**Purpose**: Build the single neutral execution source, locked wire schemas,
and principal-gate API that both stories consume.

- [ ] T003 Extract and export `NeutralTaskV1`, `NeutralRunOptionsV1`, `NeutralExecutionV1`, `runTasksV1`, `readExecutionsV1`, `stopRunV1`, and `recoverRunV1` with argv-only launch, dependency scheduling, logs, timeout, lock, artifact-root, stop, and recovery behavior in `packages/neutral-execution/src/index.ts`, `packages/neutral-execution/src/types.ts`, `packages/neutral-execution/src/process.ts`, `packages/neutral-execution/src/scheduler.ts`, `packages/neutral-execution/src/storage.ts`, `packages/neutral-execution/src/run.ts`, and `packages/neutral-execution/src/recovery.ts`; rewire `src/runner.ts`, `src/scheduler.ts`, and `src/commands/run.ts` to consume the package or remain thin compatibility exports, with no second execution implementation.
- [ ] T004 [P] Implement strict Pydantic common/mode-specific Plan and computation models, the four named Plan profiles, the request/result binding, and an explicit unsupported-mode dispatcher in `python/analytics_worker/contracts.py` and `python/analytics_worker/__main__.py`.
- [ ] T005 Add a deterministic Pydantic schema exporter with a `--check` drift mode and checked JSON Schema snapshots for every Plan profile and computation mode; add shared positive/negative vectors, Python Pydantic checks, and a TypeScript test that validates those same vectors against the generated schemas with Ajv 2020-12. Keep TypeScript validation schema-driven rather than maintaining a second handwritten contract. Own `python/analytics_worker/schema_export.py`, `src/analytics/schemas/`, `python/tests/contract-vectors/`, `python/tests/test_contracts.py`, and `src/analytics/contract-vectors.test.ts`.
- [ ] T006 [P] Implement configured `PrincipalV1`/`SourceGrantV1` resolution and the production entrypoint wrappers `buildAuthorizedWorkerContext`, `withAuthorizedSourceOperation`, `withAuthorizedCatalogQuery`, and `withAuthorizedViewerRead` in `src/analytics/principals.ts`; have each wrapper deny before its protected callback, and preserve actual caller/operation identity for controller, source, catalog, and viewer requests.

## Phase 3: User Story 1 - Start an analytics worker (Priority: P1)

**Goal**: A configured worker receives a question in a product-specific,
access-filtered workspace, and the boundary cannot route analytics outcomes
through factory retry/review behavior.

**Independent Test**: Start the analytics worker with a neutral data question;
inspect the exact supplied context, ensure ungranted source/catalog/viewer
callbacks are not entered, execute one controller-issued neutral command, and
inspect its context/outcome record. A worker shell that can escape the staged
workspace is labeled workflow-level.

- [ ] T007 [US1] Implement the sole analytics call to `runTasksV1` with retries `0`, `maxAttempts: 1`, review/repair rounds `0`, empty fallback, final review off, contradiction rejection, and no shell interpolation in `src/analytics/runner-adapter.ts`.
- [ ] T008 [US1] Export `startAnalyticsWorkerV1` as the default analytics worker launch entrypoint in `src/analytics/context.ts` for the forthcoming 003 CLI, then invoke that entrypoint with authenticated principal resolution, authorized context compilation, a synthetic source-adapter metadata read through `withAuthorizedSourceOperation`, a catalog reference lookup through `withAuthorizedCatalogQuery`, an allowlisted staged worker workspace, harness/model/version and digest recording, and a controller-run neutral command through `src/analytics/runner-adapter.ts`; use the configured `gpt-6-luna`/`xhigh` profile for the live fixture and produce a readable local inspection record at `artifacts/neutral-execution/acceptance/001-live-demo.md`.
- [ ] T009 [US1] Remove inherited factory product modules, command routes/help, prompts, fixtures, examples, task templates, and factory default-viewer language/routes from `src/factory/`, `src/commands/factory.ts`, `src/commands/index.ts`, `src/cli.ts`, `src/server.ts`, `src/server-view.ts`, `src/http.ts`, `src/commands/viewer.ts`, `viewer/index.html`, `viewer/project.html`, `viewer/run.html`, `scripts/seed-factory-dag.mjs`, `.github/copilot-instructions.md`, `.claude/skills/dag-orchestrator/SKILL.md`, `.cursor/rules/dag-orchestrator.mdc`, `skill/SKILL.md`, `skill/README.md`, `skill/install-skill.mjs`, `docs/software-factory.md`, `docs/specs/software-factory-architecture.md`, and `docs/specs/software-factory-v0.1.md`. Rewrite `README.md` to identify only the current analytics boundary, distinguish live 001 behavior from planned capabilities, link the transitional-behavior inventory, and remove references to the retired skill installer. Write `artifacts/neutral-execution/acceptance/001-transitional-behavior-inventory.md` naming every inherited behavior active in the fixture and distinguishing it from build-runner-only behavior, removed factory behavior, and analytics capabilities not yet implemented. Retain generic neutral execution compatibility and leave `scripts/build/`, `.dag-build/`, `docs/build/`, accepted feature specs, and checklists intact. Keep `src/commands/index.ts` owned by 001: after removing the factory route, register an `analytics` command trampoline that lazily imports `src/analytics/cli.js` only when invoked and calls its exported `analyticsCmd(argv, cmd)`. Use a computed import specifier so 001 builds before 003 creates that module; before 003 lands, report only an explicit analytics-handler-unavailable error. Do not swallow other import or handler failures. T009 tests the route is present and the factory route absent; 003 later supplies the handler without editing index.ts.
- [ ] T010 [US1] Add `acceptance:001` and `acceptance:001:live` scripts in `package.json`, then add production-path acceptance fixtures in `src/analytics/acceptance/001.test.ts` that start the worker through `startAnalyticsWorkerV1`, execute the context compiler's real source metadata and catalog lookup ports, and open the produced demo artifact through `withAuthorizedViewerRead`, using two principals; prove explicit allow/deny behavior, current viewer rechecks, the truthful shell-boundary label, zero factory discovery in the checkout's normal discovery paths and staged worker workspace, and a user-inspectable question/context/neutral outcome. Read the transitional-behavior inventory and README and assert that all fixture-active inherited behaviors are named, build-runner behavior is separated from analytics runtime behavior, and unimplemented analytics capabilities are not presented as live. Include runner-success/failed-check and valid-negative fixtures that retain their analytics state and produce zero retry, review redo, repair, or fallback. Include denied sheet-operation and operation-mismatch fixtures: a principal with only metadata permission MUST be denied a sheet read; verify deny-by-default for an ungranted sheet operation through the real grant gate before any read.

## Phase 4: User Story 2 - Reuse reliable execution (Priority: P2)

**Goal**: Preserve neutral execution behavior under one source package and
provide a tested path for the legacy software product to consume that exact
package without remote publishing.

**Independent Test**: Run the same neutral vectors through the extracted
package and legacy adapter and compare dependency order, argv, exit status,
logs, timeout, stop/cancellation, locks, paths, crash recovery, and replay.
Build the local archive and verify its digest from the produced bytes.

- [ ] T011 [US2] Port and run the existing neutral runner/scheduler regression coverage as the frozen conformance suite against the extracted package and tested legacy adapter in `packages/neutral-execution/src/legacy-adapter.ts`, `packages/neutral-execution/tests/conformance.test.ts`, `packages/neutral-execution/tests/vectors/`, `src/runner.test.ts`, and `src/scheduler.test.ts`.
- [ ] T012 [US2] Build the local neutral package archive and compute its actual SHA-256 manifest without remote publishing in `packages/neutral-execution/scripts/pack-local.mjs` and `artifacts/neutral-execution/`.

## Phase 5: Polish and Complete Acceptance

**Purpose**: Verify every feature criterion and record evidence for handover.

- [ ] T013 Run the complete 001 acceptance, including three recorded `gpt-6-luna`/`xhigh` worker starts with different neutral questions, the local neutral command, all four caller gates, negative/failed-check no-retry cases, the README/inventory checks, the T53 checkout/context discovery-path search, Python/TypeScript vector conformance, legacy compatibility, package archive digest, and `python scripts/audit_spec_coverage.py`; write `specs/001-isolate-analytics-product/verification.md` with actual evidence for each FR, SC, Edge Case, and T53. Record SC-006/FR-007 evidence for the transitional-behavior inventory and SC-008 evidence for the ungranted sheet request and the metadata-versus-sheet operation mismatch.

## Dependencies and Execution Order

### Phase dependencies

- **Setup (T001-T002)**: No feature-code dependencies. The two package
  bootstraps have disjoint files and may proceed in parallel.
- **Foundation (T003-T006)**: T003 depends on T001; T004 depends on T002;
  T005 depends on T004 and T001 (for the Pydantic models and Ajv workspace
  dependency); T006 depends on T001. T003/T004/T006 touch disjoint
  implementation areas.
- **User Story 1 (T007-T010)**: Depends on the neutral package, common wire
  contracts, and access gateways. T008 is the default launch integration;
  T010 verifies each production caller rather than only policy helpers.
- **User Story 2 (T011-T012)**: Depends on the extracted package. It tests
  compatibility and packages the archive from the authoritative source.
- **Final acceptance (T013)**: Depends on both stories and records
  `verification.md`; no implementation task is marked complete during
  planning.

### Story dependencies

- **US1 (P1)**: Starts after the neutral package, contracts, and principal
  gateways are ready. T009 produces the README and transitional-behavior
  inventory that T010 validates. It delivers the default worker question
  path, explicit retry boundary, product cleanup, and inspected result.
- **US2 (P2)**: Starts after the neutral package source exists. It proves the
  legacy compatibility adapter and local release archive.
- The final unit depends on both stories. User stories are ordered in the
  manifest to protect their shared package/context surfaces.

### Parallel opportunities

- T001 and T002 use separate package trees.
- After T001/T002, T003, T004, and T006 can be implemented in parallel because
  they own distinct package or source files. T005 follows T004.
- T011 can be developed against T003 while US1 context work proceeds, though
  feature units are serialized by the taskflow manifest.

## Execution Units

The executable mapping is in [execution.json](execution.json). It gives every
task one owner unit, every requirement and success criterion at least one
task, and every Edge Case an `EC-001`–`EC-004` mapping in specification order.
Each unit has a runnable check; the last unit runs the complete acceptance and
writes `verification.md`.

## Implementation Strategy

1. Establish the TypeScript/Python workspace and neutral package/contracts.
2. Add the shared principal caller gates and analytics adapter.
3. Integrate the default worker launch and clean inherited product discovery
   surfaces; prove the user-visible context/outcome path.
4. Run legacy parity and produce the local neutral archive/digest.
5. Run all FR/SC/edge and T53 checks, then write an evidence-based
   `verification.md`.

The smallest independently inspectable increment is US1 after the package and
contract foundation. Do not describe later statistical computation, evidence
publication, answer memory, or the 011 browser trace page as implemented.
