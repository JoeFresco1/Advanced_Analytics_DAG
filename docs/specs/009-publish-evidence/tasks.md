# Tasks: Publish and Recover Exact Evidence

**Input**: Design documents from `specs/009-publish-evidence/` and the accepted `spec.md`.

**Prerequisites**: `plan.md`, `spec.md`, `research.md`, `data-model.md`, `contracts/publication-v1.md`, `quickstart.md`.

**Organization**: Tasks preserve the two P1 user stories. The local execution units in `execution.json` serialize shared-file work and provide directly runnable focused acceptance commands.

**Scope**: Create only the graph-owned outputs `src/analytics/store.ts`, `src/analytics/publication.ts`, `src/analytics/stages.ts`, `src/analytics/acceptance/009.test.ts`, and final `specs/009-publish-evidence/verification.md`. Continue using node 001's runner adapter. Do not edit `dag.run.json`, shared architecture/interface files, package manifests, accepted specs/checklists, or other features.

## Phase 1: User Story 1 — Open the computation (Priority: P1)

**Goal**: Every Result and artifact opens through an exact immutable computation binding, and run publication exposes only complete, validated evidence.

**Independent Test**: In an isolated synthetic run sidecar, verify that a complete fixture commits and that missing output, a stale artifact subject, altered bytes, same-revision writers, pre-commit interruption, and lost acknowledgement produce the specified durable outcomes.

- [ ] T001 [US1] Implement the shared scope-aware append-only/CAS persistence primitive in `src/analytics/store.ts`: separate run/project sidecar roots and writer locks, immutable record/artifact/commit writes, canonical request digest, monotone revision chain, expected-revision arbitration, and atomic manifest-pointer visibility. Keep neutral run storage untouched. **Depends on** graph prerequisites 001 and 18-bootstrap.
- [ ] T002 [US1] Implement `RunManifestV1` in `src/analytics/store.ts`; implement `commitEvidence` and `recoverEvidence` in `src/analytics/publication.ts` over the shared primitive. Reject missing or altered declared outputs and mismatched exact Plan/program/environment/source/attempt/execution bindings; retain failed execution evidence. Recheck the principal's current run-commit write authorization at the publication boundary; reject a missing/denied grant and a grant revoked after staging without advancing the manifest or exposing staged records. Reject same-ID changed-content reuse. Add run-scope fixtures in `src/analytics/acceptance/009.test.ts` named `f009-t01-zero-exit-missing-output`, `f009-altered-output-digest`, `f009-t02-check-result-older-artifact`, `f009-t13-crash-before-manifest`, `f009-t14-run-writers-same-revision`, `f009-t52-commit-ack-lost`, `f009-run-scope-commit-id-changed-content-rejected`, `f009-run-scope-write-denied-at-commit`, and `f009-run-scope-write-revoked-before-commit`. Assert T02's old check is rejected for its older artifact subject and never reused; denied, revoked, and collision rejections leave the committed head unchanged. **Depends on** T001.

**Unit checkpoint**: The `run-evidence` unit compiles and its `run-scope` tests execute directly from `009.test.ts`, including current write-grant denial and revocation at commit time; those cases assert no new visible revision. It does not defer store race/recovery or access checks to a later unit.

## Phase 2: User Story 2 — Recover disruption (Priority: P1)

**Goal**: Project memory is append-only and replayable, and it can reference only already committed run evidence.

**Independent Test**: Race project writers at one expected revision; interrupt and replay publication; verify same-ID/same-content replay, same-ID/changed-content rejection, and rejection of an uncommitted run reference.

- [ ] T003 [US2] Implement `ProjectManifestV1`, `ProjectCommitRequestV1`, `ProjectCommitResultV1`, `commitProjectMemory` and `recoverProjectMemory` in `src/analytics/store.ts` and `src/analytics/publication.ts`, reusing the shared scoped CAS primitive. Recheck current project-memory write authorization and access to every referenced committed run record before publication; reject missing/denied write access, a write grant revoked after staging, or denied/revoked access to referenced evidence without advancing the project head. Add `project-scope` fixtures in `src/analytics/acceptance/009.test.ts` for concurrent writers, pre/post-commit interruption and recovery, lost acknowledgement, same-ID idempotent replay, same-ID changed-content rejection, uncommitted cross-run reference rejection, `f009-project-scope-write-denied-at-commit`, `f009-project-scope-write-revoked-before-commit`, `f009-project-scope-reference-access-denied`, and `f009-project-scope-reference-access-revoked-before-commit`. **Depends on** T002.

**Unit checkpoint**: The `project-memory` unit compiles and executes its `project-scope` tests by name from `009.test.ts` before catalog consumers arrive, including current write-grant denial/revocation and referenced-run access denial/revocation; rejected writes leave the project head unchanged.

## Phase 3: Stage binding, reproduction, and availability

**Goal**: The production single-stage path records exact environment provenance and output identity; reproduction comparison and current artifact availability remain distinct.

**Independent Test**: Invoke a synthetic one-stage fixture through node 001's adapter and verify pinned binding/output publication, then compare byte-changed but value-equal serialization, a numeric mismatch, unavailable evidence, and redacted evidence.

- [ ] T004 [US1] Implement `StageRequestV1`, `StageResultV1`, and `executeStageV1` in `src/analytics/stages.ts` using the node-001 runner adapter only. Validate execution/attempt/Plan/input/program/environment refs, declared output schema/path/digest and process result; record the execution before publication and return a bounded result without analytical-result retries. Add the reused earlier-execution-path/different-attempt fixture to `src/analytics/acceptance/009.test.ts`. **Depends on** T003.
- [ ] T005 [US2] Implement durable reproduction assessment and artifact availability handling in `src/analytics/publication.ts`: record exact-byte, canonical-table, or numeric-tolerance comparison with the canonical reproduction state; preserve code/Python/dependency-lock/environment provenance; retain an access-safe historical record when availability becomes redacted or unavailable. Add `stage-and-comparison` fixtures in `src/analytics/acceptance/009.test.ts` for T15 serialization change with equal values, a real numeric mismatch, all four reproduction states, and T22 post-publication redaction. **Depends on** T004.

**Unit checkpoint**: The `stage-and-comparison` unit compiles and runs its stage and comparison fixtures independently.

## Phase 4: Final acceptance and verification

**Purpose**: Exercise the complete owned boundary, report measured evidence against every accepted FR/SC, and leave the implementation checklist unchanged.

- [ ] T006 [US1] [US2] Complete the integrated synthetic acceptance matrix in `src/analytics/acceptance/009.test.ts` for run and project scope, exact bindings, zero-exit/missing output, altered evidence, T01/T02/T13/T14/T15/T22/T52, both race paths, same-ID replay and changed-content collision, pre-commit and post-commit crash recovery, cross-run reference rejection, current run/project write authorization with denied and revoked grants, denied and revoked access to referenced run evidence, environment provenance, semantic comparison, and redaction. Run the focused 009 test file and the full `pnpm test` acceptance command; write `specs/009-publish-evidence/verification.md` with actual command results and one evidence entry for every FR-001–FR-006 and SC-001–SC-005. Keep negative/null/failure outcomes intact; do not change a threshold, expected answer, or acceptance requirement. **Depends on** T001–T005.

## Proposal T-case ownership

| Proposal case | Focused task / fixture |
| --- | --- |
| T01 — Exit zero without declared result | T002 / `f009-t01-zero-exit-missing-output` |
| T02 — Check result refers to older artifact | T002 / `f009-t02-check-result-older-artifact` |
| T13 — Crash before manifest commit | T002 / `f009-t13-crash-before-manifest` |
| T14 — Same-revision writers race | T002 and T003 / run- and project-scope race fixtures |
| T15 — Serialization changes, values equal | T005 / `f009-t15-semantic-serialization` |
| T22 — Evidence disappears or is redacted | T005 / `f009-t22-redacted-evidence` |
| T52 — Commit succeeds, acknowledgement is lost | T002 and T003 / run- and project-scope lost-ack fixtures |

## Requirement and edge-case coverage

The task IDs below are feature-local checklist items. `T01`–`T61` above are separate proposal cases.

| Requirement | Primary check from accepted FR map | Implementation task |
| --- | --- | --- |
| FR-001 | SC-004 | T002, T004 |
| FR-002 | SC-001 | T002, T004 |
| FR-003 | SC-002 | T001, T002, T003 |
| FR-004 | SC-002 | T002, T003, T006 |
| FR-005 | SC-004 | T005, T006 |
| FR-006 | SC-004 | T005, T006 |

The frozen publication interface also requires current authorization checks at
commit time. T002 owns run-write grant denial/revocation fixtures; T003 owns
project-write grant denial/revocation and referenced-run access fixtures; T006
runs the complete set in the integrated acceptance pass. These interface
checks supplement the accepted FR/SC/edge maps without changing the feature
specification.

| Success criterion | Evidence-producing tasks |
| --- | --- |
| SC-001 | T002, T006 |
| SC-002 | T002, T003, T006 |
| SC-003 | T005, T006 |
| SC-004 | T004, T005, T006 |
| SC-005 | T004, T006 |

| Edge ID | Edge case in accepted spec order | Owning task |
| --- | --- | --- |
| EC-001 | A completed command writes no declared output. | T002 |
| EC-002 | A worker reuses an earlier execution's path. | T004 |
| EC-003 | A crash occurs after commit but before the worker receives confirmation. | T002, T003, T006 |
| EC-004 | Serialization changes while values remain equal. | T005 |
| EC-005 | Evidence is later redacted under retention rules. | T005, T006 |

## Dependencies and execution order

Feature prerequisites `001` and `18-bootstrap` come from `docs/build/graph.json` and are supplied by the build compiler. Local units serialize in this order:

1. `run-evidence` — T001, T002; no local prerequisite.
2. `project-memory` — T003; depends on `run-evidence`.
3. `stage-and-comparison` — T004, T005; depends on `project-memory`.
4. `final-acceptance` — T006; depends on `stage-and-comparison` and writes `verification.md`.

Tasks that touch the same module or shared acceptance file stay in the same unit or in this listed sequence. No task is marked parallel because implementation order protects the shared publication boundary.

## Parallel example

There are no safe parallel tasks in this feature: all implementation tasks modify the same store/publication contract or its shared acceptance file. The build may run independent feature work in other isolated worktrees after planning review.

## Implementation strategy

1. Establish run-scope CAS and recovery first, with directly runnable run race and crash fixtures.
2. Add project scope through the same primitive and verify project races and cross-run reference checks before catalog consumers arrive.
3. Add the node-001-backed stage coordinator, exact environment provenance, semantic reproduction and availability/redaction behavior.
4. Run the complete focused feature acceptance, then record actual FR/SC evidence in `verification.md`.

All checklist boxes remain unchecked in this planning stage.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
