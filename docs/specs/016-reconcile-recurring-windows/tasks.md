# Tasks: 016 â€” Recurring windows, comparability and dependency invalidation

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## window-identity-comparison

- [ ] T001 [P] Add synthetic source, window, record-ref, and 009 persistence fixture builders in `src/analytics/acceptance/016.test.ts`.
- [ ] T002 [P] Define V1 window identity/version, comparison reason, inspection policy/look, correction, and dependency-impact types in `src/analytics/recurrence.ts`.
- [ ] T003 [US1] Add focused recurrence and period-comparison assertions for immutable versions, complete window identity, canonical states, and reason refs in `src/analytics/acceptance/016.test.ts`.
- [ ] T004 [US1] Implement stable logical window identity, immutable recurrence versions, comparison assessments/reasons, and orientation of prior windows in `src/analytics/recurrence.ts`.

## impact-traversal

- [ ] T006 Implement cycle-safe traversal of all nine canonical impact categories and append-only impact commits in src/analytics/invalidation.ts. Consume synthetic committed dependency records and 009; preserve historical nodes, exact reasons and revision refs. Test all categories, cycles, two corrections and commit replay/failure through this owned production entry point. Do not require recurrence hooks or the later 005 demo.

## scope-t005-t008

- [ ] T005 [US2] Add synthetic correction, provisional-report completeness/lateness, event/processing-time, repeated-look, registry-lifecycle, and answer/trace acceptance cases in `src/analytics/acceptance/016.test.ts`.
- [ ] T008 Extend src/analytics/recurrence.ts provider hooks to record inspection looks under the declared policy, route corrections through the accepted invalidation traversal, commit 009 refs and expose them to existing answer/trace finalization. Cover event/processing time, lateness/completeness, repeated looks, lifecycle dispatch and durable failure/replay. Consume only opaque answer-card/ledger refs rather than requiring future demos.

## acceptance-verification

- [ ] T007 Record every FR/SC and edge-case result, command outcome, evidence refs, and fixture limitations in `specs/016-reconcile-recurring-windows/verification.md` after the declared checks pass.

## Dependencies

| Unit | Depends on |
| --- | --- |
| window-identity-comparison | Completed feature prerequisites |
| impact-traversal | window-identity-comparison |
| scope-t005-t008 | impact-traversal |
| acceptance-verification | scope-t005-t008 |

## window-comparison


## Capability amendment — 2026-10-10

The execution manifest owns amended unit order and review scope. The preserved original task text remains a whole-feature obligation; split units jointly satisfy it. New corrections have their own checklist IDs.
