# Feature 021 Verification Plan

**Status**: Planning baseline only. Product implementation and acceptance evaluation have not been run in this task-plan stage. T008 owns replacing this plan with measured results; no criterion is claimed passed here.

## Planned commands and evaluator

- `pnpm build` checks the provider against the frozen TypeScript registry types.
- `pnpm exec tsx --test --test-name-pattern=capture-US1 src/analytics/acceptance/021.test.ts` runs the focused method-endorsement and answer-memory cases.
- `pnpm exec tsx --test --test-name-pattern=capture-US2 src/analytics/acceptance/021.test.ts` runs the repeated-work, costly-discovery, correction, scope, conflict, exposure, and allowance cases.
- `pnpm exec tsx --test src/analytics/acceptance/021.test.ts` runs all 021 acceptance cases.
- The 48-signal batch is pinned and adjudicated through the 18-bootstrap `src/analytics/evaluation/registry.ts` API after capture output is recorded. It retains eight positives for each trigger, eight negative answer-only praise signals, and eight negative unvalidated-significance signals. Evaluator-only references are resolved after each completed response and never enter capture context. The report must show proposal IDs, true-positive count, total-proposal count, positive-signal count, precision, recall, and duplicate-proposal count. Labels, lock revision, metric-registry revision, policy version, and gates are read-only.

## Requirement evidence register

All rows are pending implementation evidence. The task mapping identifies the planned producer/test.

| Requirement | Planned evidence | Status |
| --- | --- | --- |
| FR-001 | Automatic 003 evidence/finalization hook dispatch; feedback from terminal-intent refs matched to committed 020 answer records; bounded completed question/answer cases (T001, T002, T008) | Pending |
| FR-002 | Typed endorsement, repeated validation, costly discovery, and validated correction evidence (T003, T005, T008) | Pending |
| FR-003 | Candidate trigger/evidence/type/audience/scope/reuse/gap record assertions (T003, T006, T008) | Pending |
| FR-004 | Answer annotation vs reusable candidate separation (T002, T008) | Pending |
| FR-005 | Duplicate suppression, revision links, and reasons (T004, T006, T008) | Pending |
| FR-006 | Negative/unvalidated/significance-only no-capture assertions (T002, T004, T008) | Pending |
| FR-007 | Fixed allowance termination with finalization capacity preserved (T004, T006, T008) | Pending |
| FR-008 | Owner conflict refs and candidate-only status (T004, T006, T008) | Pending |
| FR-009 | 18-bootstrap pinned fixture/evaluator API, post-response labels, locked policy version, denominators, and capture metrics (T007, T008) | Pending |

## Success criteria evidence register

| Criterion | Planned evidence | Status |
| --- | --- | --- |
| SC-001 | Endorsement checkpoint yields one traceable candidate (T002, T003, T008) | Pending |
| SC-002 | Two independent validated uses yield one candidate with both uses (T004–T006, T008) | Pending |
| SC-003 | Answer-only praise annotation; zero method candidate; no unvalidated activation-eligible candidate (T002, T008) | Pending |
| SC-004 | Repeated trigger batch ends within allowance and final reporting proceeds (T004, T006, T008) | Pending |
| SC-005 | Conflicting owner definition is named and no activation transition occurs (T004, T006, T008) | Pending |
| SC-006 | Costly discovery and validated later correction produce fully populated scoped candidates (T004–T006, T008) | Pending |
| SC-007 | Identical signal is visibly suppressed; changed procedure is a linked revision (T004, T006, T008) | Pending |
| SC-008 | Holdout exposure remains linked; single-role/period scope is exact (T004, T006–T008) | Pending |
| SC-009 | Frozen 48-signal report: ≥10 proposals, precision ≥0.80, recall ≥0.80 over 32 positive signals, zero duplicates; missing labels/denominators fail (T007, T008) | Pending |

## Ordered edge-case evidence register

| Edge | Spec case | Planned test | Status |
| --- | --- | --- | --- |
| EC-001 | Praise is for the answer, not its method/rule | Answer annotation and zero method candidate (T002, T008) | Pending |
| EC-002 | Calculation conflicts with owner-curated definition | Candidate names definition/owner and remains candidate (T004, T006, T008) | Pending |
| EC-003 | Prior holdout outcome is in proposed body | Outcome stays scoped with exposure; no generic instruction (T004, T006–T008) | Pending |
| EC-004 | Candidate work exceeds memory allowance | Checkpoint stops within policy budget and retains finalization (T004, T006, T008) | Pending |
| EC-005 | Procedure only applies to one role or period | Candidate records that exact scope and no broader scope (T004, T006, T008) | Pending |

## Final record fields to replace this plan

T008 records actual command output, test case counts, pinned 18-bootstrap evaluator/fixture and metric-registry versions, all 48 signal labels/denominators, measured precision/recall/duplicates, per-FR/SC/EC evidence references, and unresolved failures. A missed threshold is recorded as a failure without changing accepted labels, lock, metric registry, or policy.
