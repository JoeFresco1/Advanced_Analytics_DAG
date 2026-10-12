# Feature 015 Verification Plan

**Status**: Pending implementation. This file is the planning-stage evidence
matrix, not a test report. No implementation command has been run in this
planning stage. The final implementation unit (T010–T011) must replace every
pending entry with the actual command result, fixture/test name, and evidence
reference. A skipped or TODO fixture is not passing evidence.

## Planned commands

| Check | Command | Expected evidence |
| --- | --- | --- |
| Build | `pnpm build` | Successful TypeScript compile and existing viewer build. |
| Feature acceptance | `pnpm exec tsx --test src/analytics/acceptance/015.test.ts` | All 015 synthetic reuse, drift, access, refresh, exposure, revocation and recipe-use assertions execute. |
| Repository tests | `pnpm test` | Existing repository build and complete Node test suite, including compiled feature 015 acceptance. |

## Requirement evidence matrix

| Requirement | Planned test/evidence binding | Status |
| --- | --- | --- |
| FR-001 | `015.test.ts`: invoke the ordinary 003 registered inquiry-orientation caller and exercise one normative predicate for source identity and environment/tenant drift, current role/grant/audience, lifecycle, required schema/key/grain, units/timezone, category/metric definitions, source-version compatibility evidence, watermark/freshness/lag and recipe privileges; required unknown, mismatch or unvalidated transition is non-current. A table-driven candidate fixture varies compatibility, role/audience and freshness, never supplies candidate instructions, and returns at most a grant-permitted access-safe review reference. Lifecycle checks cover candidate, active, stale and revoked. Cadence assertions confirm no expensive profile before due time without drift, a profile when cadence is due or relevant drift triggers it, and no current use while that exact profile is pending. | Pending |
| FR-002 | `015.test.ts`: through the registered 003 caller, inquiry pins the exact skill version and lists supplied references separately from acknowledged executed recipes. | Pending |
| FR-003 | `015.test.ts`: changed bindings stale only dependent guidance; immutable refresh creates a successor; revocation remains 022-owned. | Pending |
| FR-004 | `015.test.ts`: after refresh, an old inquiry retains its exact original skill/version/result, current validity marks that version `stale`, and orientation supplies only a validated active compatible replacement. The replacement is read-only historical fixture setup until 022 owns production activation. | Pending |
| FR-005 | `015.test.ts`: study/holdout summary remains a separate reference and creates an exposure event when supplied. | Pending |
| FR-006 | `015.test.ts`: narrowed grant yields no protected recipe/reference in current worker context. | Pending |

## Success criteria evidence matrix

| Criterion | Planned test/evidence binding | Status |
| --- | --- | --- |
| SC-001 | `015.test.ts`: two-inquiry cold/warm path through the ordinary 003 caller and extension registry; exact skill pin, at least one executed recipe, fewer discovery steps, and set-equality assertion that the warm path retains every required check from the current policy. | Pending |
| SC-002 | `015.test.ts`: same-schema key/grain, unit, role/audience, timezone, lag/watermark and category/metric-definition changes stale affected items; unknown required facts remain unresolved; an unused-column addition preserves unrelated guidance. | Pending |
| SC-003 | `015.test.ts`: every supplied study-specific outcome has an exposure record. | Pending |
| SC-004 | `015.test.ts`: explicit post-refresh path preserves the earlier inquiry's exact pin/result, marks the old version stale for current use, and supplies only a validated active compatible replacement (seeded as fixture state with independent validation/authority refs before 022 exists). | Pending |
| SC-005 | `015.test.ts`: current narrow grant removes former-access recipes/references, even if a version remains active for another role. | Pending |
| SC-006 | `015.test.ts`: the same predicate detects seeded source identity, environment/tenant, source-version evidence, unknown/unvalidated transition, key/grain, unit, role/grant, timezone, metric-definition and watermark/lag cases; unused column remains compatible. Candidate status blocks active instruction supply across compatibility, role/audience and freshness variants; all four canonical lifecycle states are checked through the registered caller. | Pending |
| SC-007 | `015.test.ts`: a read-only synthetic authority-revoked historical record preserves the old inquiry's pin, a new orientation receives no current guidance from it, and a supplied-but-unexecuted recipe has no executed-use event. No activation/revocation transition is implemented by 015. | Pending |

## Edge-case evidence matrix

Edge IDs preserve `spec.md` bullet order and `docs/specs/edge-verification-map.md` ownership.

| Edge | Planned test/evidence binding | Status |
| --- | --- | --- |
| EC-001 — shared holdout summary | `015.test.ts`: outcome provenance and exposure remain visible when summary is supplied. | Pending |
| EC-002 — access rights narrow | `015.test.ts`: former-access recipes and references do not enter current worker context. | Pending |
| EC-003 — historical pin to revoked version | `015.test.ts`: old pin remains exact; new orientation receives no current guidance from it. | Pending |
| EC-004 — reference shown, recipe not followed | `015.test.ts`: supply event exists; executed-recipe event is absent. | Pending |

## Proposal acceptance cases

| Case | Planned evidence |
| --- | --- |
| T18 | T005/T006/T007/T009: same-schema unit, key/grain, role, timezone, freshness and definition changes produce scoped `stale` behavior; post-refresh orientation supplies only a validated compatible replacement or preserves the unresolved assumption. |
| T19 | T001/T009: runnable `pnpm exec tsx --test src/analytics/acceptance/015.test.ts` cases invoke the ordinary 003 registered inquiry-orientation caller; warm reuse records the exact version and supplied/consumed references or recipes, reduces discovery, and asserts the complete required-check set is retained unchanged. |
| T25 | T009: runnable `015.test.ts` case invokes the ordinary 003 registered orientation caller; an allowed shared outcome summary retains outcome provenance, writes an exposure event when supplied, and cannot be relabeled pre-observation; an injected enforced boundary keeps a protected outcome out of worker context while closed. |
