# Tasks: 017 â€” Claim use authorization and lightweight Decisions

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001-t002-t003

- [ ] T001 [P] Add synthetic Claim, Result, Check, Review, Plan, policy, actor and 009 commit/recovery fixture builders in `src/analytics/acceptance/017.test.ts`.
- [ ] T002 [P] Define `UseAuthorizationPolicyV1`, exact binding, review provenance, trace-seen and Use Assessment V1 types in `src/analytics/authorization.ts`.
- [ ] T003 [P] Define the immutable `DecisionV1` pointer shape and its required exact Claim/assessment reference fields in `src/analytics/decisions.ts`.

## scope-t004-t005

- [ ] T004 [US1] Add focused acceptance scenarios for internal uncertainty, owner policy registration, trigger selection, exact binding, trace presentation, review provenance and configured-authority gating in `src/analytics/acceptance/017.test.ts`; name the provenance case `worker versus independent review attribution and configured authority gate`.
- [ ] T005 [US1] Implement owner-controlled use-policy evaluation, assessment/review transitions and exact binding checks in `src/analytics/authorization.ts`.

## decision-change-integration

- [ ] T006 [US2] Add acceptance scenarios for risk acceptance, opposing Results, changed bindings, authority revocation, publication triggers, Decision validation and 009 failure/recovery in `src/analytics/acceptance/017.test.ts`.
- [ ] T007 [US2] Export `analyticsExtensionV1` with owner policy registration, use commands and `beforeFinalize`, persist records through 009, and implement Decision creation/current-assessment validation in `src/analytics/authorization.ts` and `src/analytics/decisions.ts`.

## acceptance-verification

- [ ] T008 Run the full 017 acceptance fixture, project build and repository tests, then record actual per-FR/SC/edge evidence and limitations in `specs/017-authorize-claim-use/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001-t002-t003 | Completed feature prerequisites |
| scope-t004-t005 | scope-t001-t002-t003 |
| decision-change-integration | scope-t004-t005 |
| acceptance-verification | decision-change-integration |
