---
description: "Implementation tasks for feature 008: Plan locking, revisions and durable exposure"
---

# Tasks: Preserve Plans and Outcome Exposure

**Input**: Approved design documents in `specs/008-plan-exposure-history/`.

**Prerequisites**: `plan.md`, `spec.md`, `research.md`, `data-model.md`, `contracts/plan-exposure-v1.md`, and `quickstart.md`.

**Tests**: The feature graph requires `src/analytics/acceptance/008.test.ts`; tests are included before the corresponding implementation tasks. This task-plan stage does not run them.

**Organization**: Setup, shared generated-schema foundation, then the two accepted user stories, followed by the feature acceptance/verification unit. Feature 008 owns its production facade and acceptance boundary; feature 003 owns full worker-context delivery and live replacement integration. The existing feature graph and shared interface freeze are unchanged.

## Phase 1: Setup

**Purpose**: Prepare synthetic/local fixtures for direct calls to the production 008 facade and 009 stage coordinator. Do not add a controller or fixture store.

- [ ] T001 Add synthetic inquiry, actor, source/partition, development outcome, evaluation metric, committed-history snapshot, and replacement-projection fixture builders in `src/analytics/acceptance/008.test.ts` using the 18-bootstrap registry IDs.

## Phase 2: Foundational

**Purpose**: Validate the frozen mode-specific wire contracts before either user story uses a Plan.

- [ ] T002 Add production-facade acceptance assertions and the exported `registerPlanV1` request/result hook backed by all four generated `PlanRegistrationV1` schemas in `src/analytics/acceptance/008.test.ts` and `src/analytics/plans.ts`; assert unsupported profiles, mismatched discriminators, missing fields, extra fields, invalid refs, and non-finite values fail closed, while valid profile fixtures pass.

## Phase 3: User Story 1 - Inspect the inquiry path (Priority: P1) 🎯 MVP

**Goal**: Preserve the question, lock a directed Plan before its execution, record exposure before showing outcomes, and retain planned search/resampling separately from an outcome-informed fork.

**Independent Test**: Call the production 008 request/result facade with synthetic fixtures and real 009 services. Inspect original wording, interpretations, locked payload digest, committed exposure, Plan timeline, release permit, and stage binding after a declared resample and an injected outcome-informed method change.

- [ ] T003 [US1] Add facade-path acceptance assertions for a declared candidate/resample, early evaluation-metric exposure, an outcome-informed method change, and `rejected`/`revision_conflict` exposure commits producing no release permit and zero `executeStageV1` calls in `src/analytics/acceptance/008.test.ts` before implementing those transitions.
- [ ] T004 [US1] Implement original-question and operational-interpretation retention, directed-plan required-field validation, and the exported `lockPlanV1` request/result hook for immutable lock, content digest, and linked revision creation in `src/analytics/plans.ts`.
- [ ] T005 [US1] Implement the exported `assessPlanChangeV1` request/result hook and deterministic locked-field comparison for population, target, method/comparison, partition design, search space, and stopping rule; retain the worker's reason and distinguish substantive forks from code-only executions in `src/analytics/plans.ts`.
- [ ] T006 [US1] Implement the exported `commitExposureBeforeReleaseV1` request/result hook and exposure construction in `src/analytics/exposure.ts`; return a release permit only for 009 `committed`/`replayed` results, block `rejected`/`revision_conflict`, and validate that every admitted search/resample belongs to the locked family. Pass T003's failure-path assertions through the production facade and real 009 stage coordinator.

## Phase 4: User Story 2 - Interpret later confirmation (Priority: P2)

**Goal**: Rebuild the complete Plan/exposure projection from a controller-supplied committed history snapshot, preserve unknown access, and prevent review approval from changing a historical label. Feature 003 consumes the projection when it constructs an actual replacement-worker context.

**Independent Test**: Supply a committed-history snapshot to the production 008 rehydration facade and confirm its result preserves original wording, Plan labels, exposure refs, completeness/unknown status, and remaining-budget binding despite a shortened summary. Add prior-outcome skill, code-only-repair, and human-approval cases. Do not simulate worker delivery; 003 owns that integration assertion.

- [ ] T007 [US2] Add acceptance assertions for the rehydration result after exposure, shortened-summary independence, a skill containing an earlier outcome, unknown out-of-tool access, code-only repair, and approval for exploratory use in `src/analytics/acceptance/008.test.ts` before implementing rehydration.
- [ ] T008 [US2] Implement validation of the committed inquiry-history snapshot supplied through 003's committed-read capability after 009 recovery, preserving only refs reachable from the committed manifest in `src/analytics/exposure.ts`.
- [ ] T009 [US2] Export typed `rehydratePlanExposureV1` request/result hooks that build a replacement-context projection from committed Question, Plan, change-assessment, Execution, and Exposure records, derive canonical labels, and mark incomplete/unknown history `exposure_unknown` in `src/analytics/plans.ts` and `src/analytics/exposure.ts`; leave actual worker-context delivery to 003.

## Phase 5: Polish and Cross-Cutting Acceptance

**Purpose**: Prove the feature-owned facade and 009 stage/persistence seams, then record evidence without claiming full controller integration or shell isolation.

- [ ] T010 Complete synthetic acceptance through the production 008 facade, 009 commit/recovery, and real `executeStageV1`; assert all 008 FRs/SCs/Edge Cases and the 008-owned T06/T07/T09/T61 producer behavior. For T61, assert the rehydration result retains exposure and labels; reserve actual replacement-worker context delivery for 003 in `src/analytics/acceptance/008.test.ts`.
- [ ] T011 Write `specs/008-plan-exposure-history/verification.md` with executed commands, actual 008 facade/stage evidence, all FR/SC/Edge Case mappings, an explicit 003 integration status (pending if the downstream feature has not run), and the local workflow-withholding limitation. Do not report the facade test as full 003 integration.

## Dependencies and Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: T001 prepares synthetic fixtures for the production 008 facade and real 009 services.
- **Foundational (Phase 2)**: T002 validates registrations against feature 001's generated schemas.
- **User Story 1 (Phase 3)**: Depends on Setup and Foundation; registers/locks Plans and records outcome exposure.
- **User Story 2 (Phase 4)**: Depends on User Story 1's immutable event and Plan records; reconstructs from committed history.
- **Polish (Phase 5)**: Depends on both stories; runs the full feature acceptance and writes the verification record.

### User Story Dependencies

- **US1 (P1)**: Independent after the schema foundation. MVP scope is the original question through a locked Plan, a planned resample, and an outcome-informed fork with durably committed exposure and release gating.
- **US2 (P2)**: Uses US1's committed record shapes to validate the rehydrated projection and approval invariance. 003 separately proves actual restart/replacement context delivery.

### Cross-Feature Dependencies

- Feature **009** supplies append-only commit/recovery and the production `executeStageV1` coordinator. 008 supplies the record payloads and uses the committed-read capability exposed by its controller host; it does not invent a 009 record-reading API.
- Feature **18-bootstrap** supplies locked synthetic records and expected case IDs; 008 adds feature-scoped assertions without changing the registry's accepted labels or thresholds.
- Feature **003** owns the production inquiry controller/CLI and consumes 008. Its acceptance must replay the same T06/T07/T09/T61 cases through the actual controller once 003 exists. The 008 graph edge stays acyclic.

### Unit Mapping

| Unit | Tasks | Main files | Completion check |
| --- | --- | --- | --- |
| `mode-registration` | T001-T002 | `src/analytics/plans.ts`, `src/analytics/acceptance/008.test.ts` | `pnpm typecheck`; `pnpm test` (valid profiles and strict-rejection cases) |
| `locked-revision-exposure` | T003-T006 | `src/analytics/plans.ts`, `src/analytics/exposure.ts`, `src/analytics/acceptance/008.test.ts` | `pnpm typecheck`; `pnpm test` (commit gate, permit, and no-stage-call cases) |
| `facade-rehydration-acceptance` | T007-T011 | `src/analytics/plans.ts`, `src/analytics/exposure.ts`, `src/analytics/acceptance/008.test.ts`, `specs/008-plan-exposure-history/verification.md` | `pnpm test` |

Units are ordered because each extends the same feature modules and acceptance path. No tasks are marked parallel: the fixture, schema, and facade assertions share the acceptance test, and later tasks build on the same Plan/exposure transitions.

## Requirement and Acceptance Coverage

### Functional Requirements

| Requirement | Tasks |
| --- | --- |
| FR-001 | T004, T009, T010 |
| FR-002 | T002, T004, T010 |
| FR-003 | T003, T006, T010 |
| FR-004 | T005, T006, T010 |
| FR-005 | T003, T006, T010 |
| FR-006 | T007, T009, T010 |
| FR-007 | T007, T008, T009, T010 |
| FR-008 | T003, T005, T007, T009, T010 |
| FR-009 | T003, T005, T010 |

### Success Criteria

| Criterion | Tasks |
| --- | --- |
| SC-001 | T006, T010 |
| SC-002 | T003, T010 |
| SC-003 | T007, T008, T009, T010 |
| SC-004 | T003, T005, T010 |
| SC-005 | T003, T007, T008, T009, T010 |
| SC-006 | T003, T006, T009, T010 |

### Edge Cases (spec order)

| Edge ID | Edge Case | Owning FR | Tasks and check |
| --- | --- | --- | --- |
| EC-001 | A source skill contains an earlier outcome and is loaded into the inquiry. | FR-003 | T007, T009, T010; T25 provenance case |
| EC-002 | A code bug is repaired without changing the comparison. | FR-008 | T003, T005, T010; SC-004 |
| EC-003 | A different harness resumes from a shortened summary. | FR-007 | T007-T010; SC-003 and T61 |
| EC-004 | A human approves post-observation work for exploratory use. | FR-006 | T007, T009, T010; SC-003 |

### Proposal Cases

- **T06**: T003/T005/T006/T010 — outcome-informed fork, new execution, prior exposure retained, approval cannot restore pre-specification. Full 003 inquiry path is downstream.
- **T07**: T003/T006/T010 — declared candidate/resample remains inside the locked plan family after earlier results.
- **T09**: T003/T006/T010 — evaluation metrics opened early produce committed exposure before release; unknown out-of-tool access is not ruled out.
- **T61**: T007-T010 — 008 rehydration result retains committed exposure and the post-observation label; actual replacement-worker context delivery is asserted by 003.
- **T25**: T007/T009/T010 — outcome provenance survives when carried in a source skill/shared summary; primary proposal ownership remains with feature 015.

## Parallel Opportunities

No tasks are independent enough to run in parallel: the acceptance fixture, strict-schema cases, exposure release gate, and rehydration assertions share the same feature test path. No separate research or external fixture work is needed.

## Implementation Strategy

1. Complete fixture and schema registration work, then run compile and strict-schema acceptance checks.
2. Deliver US1 as the MVP: typed profiles, immutable lock/fork, explicit commit-before-release gating, and planned candidate/resample behavior through 009 `executeStageV1`.
3. Deliver US2: committed-history validation and a replacement-context projection with no label reset.
4. Run all 008 acceptance tests and complete `verification.md`; 003 later composes the exported APIs and tests actual worker-context delivery through its production controller.

## Task Format Validation

There are 11 unchecked tasks. Every task has a unique sequential `T001`-style ID and an exact file path; no task is marked parallel, Setup/Foundation/Polish tasks omit story labels, and every User Story phase task has its required `[US1]` or `[US2]` label. No task is marked complete in this planning stage.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
