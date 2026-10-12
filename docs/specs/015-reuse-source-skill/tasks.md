# Tasks: 015 â€” Source-skill compatibility, freshness and refresh

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t002

- [ ] T002 [US1] Implement the single complete `compareSourceSkillCompatibilityV1` predicate for source/environment/tenant identity, grant/audience/role, lifecycle, relevant schema/key/grain meaning, units/timezone, definitions, source-version evidence, freshness/watermark/lag, and recipe privileges in `src/analytics/skill-compatibility.ts` (depends on T001).

## scope-t001-t003-t004

- [ ] T001 [US1] Add synthetic cold/warm inquiry assertions in `src/analytics/acceptance/015.test.ts` by invoking the ordinary 003 production inquiry-orientation caller with the registered extension loader and real dependency ordering (not by calling the 015 provider directly or checking metadata alone); assert exact skill version, entrypoint and reference supply, actual recipe execution, separate supplied/executed events, reduced discovery steps, set-equality retention of every required check from the current policy, and durable project-write requests through the 009 context service. Any pre-022 active skill is read-only historical fixture setup with validation/authority refs, never a production activation.
- [ ] T003 [US1] Export `analyticsExtensionV1` as `source-reuse` depending on `source-discovery`; orient only to an exact compatible entrypoint/reference set and persist supplied-version/reference and acknowledged recipe-execution events through 003 context and 009 project writes in `src/analytics/skill-refresh.ts` (depends on T002).
- [ ] T004 [US1] Run `pnpm build` and `pnpm exec tsx --test src/analytics/acceptance/015.test.ts`; resolve the User Story 1 assertions in `src/analytics/acceptance/015.test.ts` (depends on T001, T002, and T003).

**Checkpoint**: The compatible second inquiry observes the same version the
provider pins, and only a recipe actually run by the execution adapter is
recorded as executed.

## scope-t005-t006

- [ ] T005 [US2] Add compatibility fixtures in `src/analytics/acceptance/015.test.ts` for source identity and environment/tenant drift; a source-version transition with declared compatibility evidence and a missing or unvalidated transition; unknown required facts; and schema-compatible changes to key/grain, unit, role/audience/grant, timezone, lag/watermark, and category/metric definitions. Assert that only guidance depending on changed or unknown facts is stale and that an unrelated added column leaves other guidance usable.
- [ ] T006 [US2] Complete the single predicate with per-guidance fact dependencies, explicit unknown/mismatch results, relevant-only invalidation, source-version compatibility evidence, and cheap checks on every orientation versus cadence/drift-triggered profile results in `src/analytics/skill-compatibility.ts`; in `src/analytics/acceptance/015.test.ts`, assert that profiling does not run before cadence without drift, runs when cadence is due or a recorded relevant drift triggers it, a pending or stale profile cannot make impacted guidance current, and a passing profile is bound to the exact source version and policy/check revision before it can establish current compatibility (depends on T005).

## scope-t007-t008

- [ ] T007 [US2] Implement append-only staleness and immutable refresh successor records through 009 project writes; retain every historical skill/inquiry pin and honor 022 activation/revocation authority so a revoked version cannot guide new work in `src/analytics/skill-refresh.ts` (depends on T006).
- [ ] T008 [US2] Run `pnpm exec tsx --test src/analytics/acceptance/015.test.ts` and resolve all User Story 2 drift, scope, cadence, and refresh assertions in `src/analytics/acceptance/015.test.ts` (depends on T005, T006, and T007).

**Checkpoint**: Changed facts block only dependent guidance until a validated
replacement is available; unrelated added fields remain usable, and previous
runs retain their exact version and result.

## scope-t009

- [ ] T009 Add runnable provider-level acceptance assertions in `src/analytics/acceptance/015.test.ts` through the ordinary 003 production inquiry-orientation caller and registered extension loader, with synthetic worker/source services at the boundary and committed project-memory effects observed through the real 009 writer (not a provider-only call or metadata-only assertion). Cover the canonical lifecycle matrix in supplied-guidance checks: `candidate` is never supplied as active instructions even when compatibility, role/audience and freshness are favorable; table-driven candidate variants independently vary compatibility, role/audience and freshness, and lookup may return only a grant-permitted access-safe candidate reference for review (or no reference when scope denies it); `active` can supply current guidance only when the full predicate passes; `stale` cannot supply current instructions and may appear only as a safe historical pointer with its limitation; `revoked` supplies zero current guidance while an old inquiry pin remains exact. This is read-only fixture setup and does not implement activation or revocation. Also cover study/holdout outcome provenance, an exposure commit when an allowed summary is supplied, no relabeling as pre-observation, and an injected enforced evaluation boundary that keeps a protected outcome out of context while closed (proposal T25); exact 003 extension metadata and 009 project commits; narrowed grants; and a supplied-but-not-executed recipe. Assert the proposal T19 warm path retains every required check. For SC-004, refresh after a unit change, assert the old inquiry still pins its original version/result and that version is currently stale, then assert orientation supplies only a validated, active, compatible replacement; with no eligible replacement, assert it supplies no current guidance and reports the unresolved assumption. Until 022 is implemented, seed active/replacement and revoked versions as read-only synthetic historical fixture state with independent validation/authority refs; this fixture setup must not simulate or claim production activation/revocation by 015 (depends on T003, T007).

## scope-t010-t011

- [ ] T010 Run `pnpm build`, `pnpm exec tsx --test src/analytics/acceptance/015.test.ts`, and `pnpm test`; capture command outcomes and test evidence for the verification record in `specs/015-reuse-source-skill/verification.md` (depends on T001, T002, T003, T004, T005, T006, T007, T008, and T009).
- [ ] T011 Replace the planning-stage pending matrix with actual FR/SC and edge-case evidence, including the exact command outcomes and fixture/test names, in `specs/015-reuse-source-skill/verification.md` (depends on T010).

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t002 | Completed feature prerequisites |
| scope-t001-t003-t004 | scope-t002 |
| scope-t005-t006 | scope-t001-t003-t004 |
| scope-t007-t008 | scope-t005-t006 |
| scope-t009 | scope-t007-t008 |
| scope-t010-t011 | scope-t009 |
