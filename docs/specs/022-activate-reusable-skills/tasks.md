# Tasks: 022 â€” Slice C2 independent activation and later skill use

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001-t002-t003

- [ ] T001 [P] [US1] Add synthetic fixtures for worker-authored method self-tests, independent policy-owned method checks, conflict outcomes, and activation record completeness in `src/analytics/acceptance/022.test.ts`.
- [ ] T002 [US1] Define immutable candidate/version/decision/use record types and enforce the canonical candidate, active, stale, and revoked lifecycle in `src/analytics/skill-activation.ts`.
- [ ] T003 [US1] Implement preapproved low-risk project-method eligibility, policy-owned independent validation, exact conflict checks, and candidate retention on missing, failed, inconclusive, or unresolved evidence in `src/analytics/skill-activation.ts`.

## scope-t004-t005

- [ ] T004 [P] [US1] Add scope and access fixtures for source, role, period, unknown predicate fields, and a skill requiring unavailable privileges in `src/analytics/acceptance/022.test.ts`.
- [ ] T005 [US1] Implement bounded orientation filters for active status, audience, current grant, freshness, and the declared method/rule scope predicate; retain source reuse on feature 015's compatibility path in `src/analytics/skill-activation.ts`.

## scope-t006

- [ ] T006 [US1] Record exact supplied skill/reference versions, actual recipe executions, and prior-outcome exposure separately, then export `analyticsExtensionV1` with scoped orientation and activation commands through 003 and project writes through 009 in `src/analytics/skill-activation.ts`.

## scope-t013-t014-t015

- [ ] T013 [US1] Add separate synthetic committed 014 source-candidate fixtures: an eligible bounded recipe with intended grant/source version, assertion provenance, and a non-required unknown definition; plus a worker-authored-replay-only candidate, an owner-conflicting candidate, and a candidate requiring a broader grant in `src/analytics/acceptance/022.test.ts`.
- [ ] T014 [US1] Implement source-candidate activation by consuming 014's source-discovery facade and 015's compatibility predicate, independently replaying the bounded recipe under its exact intended grant with a validator/execution not authored by the candidate author, checking source facts/provenance/scope/conflicts, recording only existing configured owner or a preapproved policy that exactly covers the source scope, preserving unresolved definitions as nonauthoritative, and publishing the immutable active version through 009 in `src/analytics/skill-activation.ts`.
- [ ] T015 [US1] Exercise candidate → independent validation → active commit through the production `analyticsExtensionV1` provider and 009 project-memory service; assert the active source decision binds candidate evidence, type/version, scope predicate, audience, existing authority, independent replay, conflict check, and reason; assert the exact version is oriented only within scope/grant, its unknown definition remains explicitly nonauthoritative, and worker-authored-only, conflicting, and broader-grant candidates stay candidate, without requiring 004 in `src/analytics/acceptance/022.test.ts`.

## scope-t007-t008

- [ ] T007 [P] [US2] Add owner-review fixtures for conflicting/new/changed definitions, expanded audience/access/release policy, and an explicit configured-owner approval with a duplicate-prompt assertion in `src/analytics/acceptance/022.test.ts`.
- [ ] T008 [US2] Implement configured-owner review and approval attribution, idempotent matching of one concrete approval, and candidate retention until independent validation and conflict checks pass in `src/analytics/skill-activation.ts`.

## scope-t009-t010

- [ ] T009 [US2] Implement affected-version invalidation to stale after source-unit or access-role changes and configured-authority withdrawal to revoked, preserving historical versions and uses in `src/analytics/skill-activation.ts`.
- [ ] T010 [US2] Add lifecycle assertions for exact stale/revoked targeting, prior-version retention, access denial, and zero current supply of stale/revoked guidance in `src/analytics/acceptance/022.test.ts`.

## inquiry-use

- [ ] T011 Add the synthetic two-inquiry method path and explicitly assert the second compatible inquiry receives the exact active method version, records its supplied reference and actually executed recipe against that version, and completes without a save/recreate prompt. Also propose one study Result as a general skill and assert it remains `candidate` with outcome provenance/exposure and is supplied to zero unrelated inquiries as an authoritative unscoped rule; cover scope, owner-approval reuse, and supplied-only versus executed use in `src/analytics/acceptance/022.test.ts`.
- [ ] T012 Run the focused/full acceptance command after prerequisites and implementation units pass, then write observed command results and the complete FR/SC/Edge evidence matrix in `specs/022-activate-reusable-skills/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001-t002-t003 | Completed feature prerequisites |
| scope-t004-t005 | scope-t001-t002-t003 |
| scope-t006 | scope-t004-t005 |
| scope-t013-t014-t015 | scope-t006 |
| scope-t007-t008 | scope-t013-t014-t015 |
| scope-t009-t010 | scope-t007-t008 |
| inquiry-use | scope-t009-t010 |
