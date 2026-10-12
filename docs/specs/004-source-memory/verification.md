# Verification Plan: Source Memory Inquiry Integration

**Status**: Planned only. This task-plan stage does not implement the product
or execute product tests. The final feature unit replaces this status with
the actual command output and recorded evidence; it must not change the
accepted specification, quality checklist, fixture/reference answers, or
policy thresholds.

## Planned commands

| Scope | Command | Acceptance evidence |
| --- | --- | --- |
| Source/first inquiry unit | `pnpm build`; `pnpm exec node --test --test-name-pattern="004 first ordinary ask" dist/analytics/acceptance/004.test.js` | Runs the first ordinary ask through 014's production `orient` hook and shared bounded discovery implementation, then executes the registered 022 `skills review` provider path and checks candidate-only/safe-ref behavior. |
| Reuse/invalidation unit | `pnpm build`; `pnpm exec node --test --test-name-pattern="004 second ordinary ask|004 compatibility|004 revocation" dist/analytics/acceptance/004.test.js` | Runs the second ask through 015's production `orient` hook, exercises the exact active ref, 011's generic linked-provenance answer/trace projection under current grants, compatibility drift, and the registered 022 revocation path. |
| Final feature | `pnpm test` | All required 004 acceptance cases execute with no skips/TODOs; `src/analytics/acceptance/004.test.ts` proves producer, consumer, inspector, and injected failures. |

The first unit's acceptance case runs an ordinary `dag analytics ask` with
`gpt-6-luna`/`xhigh` against the local synthetic source. Its cold-source 014
`orient` hook calls the same bounded implementation as `source discover`,
commits the candidate, and returns only its safe ref. The fixture then
independently replays a bounded recipe and invokes 022's registered
`skills review` command through 003's normal typed command registry with the
configured owner. Only after 022 commits a validated active version does the
second unit's ordinary ask use 015's registered `orient` hook. That unit
opens answers/traces through 011's existing `answer show --open` and
`trace --claim` paths and exercises
`GET /api/analytics/inquiries/{inquiryId}/answer` and
`GET /api/analytics/claims/{claimId}/trace`. It records exact inquiry,
source, skill/status, validation, authority, supplied-reference,
executed-recipe, and exposure refs through the generic linked-provenance
projection under current grants; it does not expose reference answers to the
worker. The same unit also runs the compatibility and 022 revocation cases,
preserving prior pins and proving no silent source/skill substitution.

## Planned requirement evidence

| Requirement | Planned observable evidence | Task |
| --- | --- | --- |
| FR-001 | Source/environment/tenant/role/audience and definition scope are pinned and grant checked. | T001, T003-T005 |
| FR-002 | Candidate package has concise entry guidance and separate schema, meaning, join/grain, time/freshness references, plus tested bounded recipes. | T003 |
| FR-003 | Every assertion shows declared, observed, tested-on-version, or inferred provenance and its source/window. | T003, T007 |
| FR-004 | Query/row/time cap stops discovery, records the exhausted bound, and prevents activation without validation. | T001, T003, T007 |
| FR-005 | Credential/raw-row/sensitive metadata sentinels remain absent or audience-filtered. | T007 |
| FR-006 | Instruction-like source content has no effect on policy, access, worker instructions, or recipe execution. | T007 |
| FR-007 | Only 022's provider can activate the exact candidate after independent bounded replay, conflict checks, and configured-owner approval; failed/missing validation or unknown/conflicting meaning stays candidate, with failure retained and no retry-until-pass loop. | T003, T007 |
| FR-008 | 015's single compatibility predicate governs reuse after provider-backed 022 activation, and the second inquiry pins the exact supplied skill revision. | T004-T006 |
| FR-009 | The existing 011 answer and trace APIs expose committed evidence links that distinguish references supplied from recipes actually executed without claiming comprehension. | T004 |
| FR-010 | Only affected guidance becomes stale on drift; old versions and locked inputs remain immutable. | T005-T006 |
| FR-011 | Study outcomes remain separate from source guidance; any outcome loaded later has an exposure record. | T004, T007 |

## Planned success-criterion evidence

| Criterion | Planned observable evidence | Task |
| --- | --- | --- |
| SC-001 | After 022 commits activation, the second independent inquiry receives the exact active guide version, supplies named references, executes a named recipe, and requires fewer discovery steps than the cold inquiry. | T004 |
| SC-002 | Same-schema meaning, role, key, tenant, and freshness mutations invalidate affected guidance before reuse. | T005 |
| SC-003 | Activated package contains zero credentials, raw sensitive rows, and untagged study outcomes. | T007 |
| SC-004 | Seeded source instructions remain inert with zero policy/access changes. | T007 |
| SC-005 | Warm inquiry does fewer discovery operations and retains required checks. | T004 |
| SC-006 | Exceeding a declared query, row, or elapsed-time budget stops discovery, records the limit, and activates no unvalidated guidance. | T007 |
| SC-007 | Assertion provenance is complete; 022 activates only after an independently replayed bounded recipe under the intended grant and configured-owner approval; otherwise state remains candidate and a failed result is retained. | T003, T007 |
| SC-008 | 022's active guide and 015 usage trace include source identity/environment/tenant/role/definitions; mismatched tenant or role fails 015 compatibility. | T003-T005 |
| SC-009 | Unused-column addition preserves unrelated active guidance; next-window join failure stales only affected recipe guidance before reuse. | T005 |

## Planned ordered Edge Case evidence

| Edge | Injected condition | Planned evidence | Task |
| --- | --- | --- | --- |
| EC-001 | Identical schemas, different metric meanings by environment. | 015 rejects current reuse; locked source remains unchanged and meaning is disclosed. | T005 |
| EC-002 | Unused column is added. | Unrelated validated assertions and recipes remain active. | T005 |
| EC-003 | Stale watermark or late correction. | Affected time/freshness guidance becomes stale; no silent current claim. | T005 |
| EC-004 | Join succeeds in one sample and fails in another window. | Only affected tested-join recipe guidance is stale before use. | T005 |
| EC-005 | Recipe needs broader source privileges than intended audience. | No read/execution occurs under insufficient grant; guidance is not supplied as current. | T007 |
| EC-006 | Sensitive metadata or examples. | Protected metadata and values are absent from package, search/context, and inspector output for the narrower audience. | T007 |

## Planning guard result

Command: `node scripts/build/check-plan.mjs 004`  
Result: `PLAN_GUARD PASS 004: 8 tasks, 3 executable units; no product-code edits`.

The manifest retains the recovered task IDs and complete FR/SC/ordered-edge
maps. The first executable unit now has a runnable acceptance command for the
014 cold-orientation discovery and 022 review flow; the second runs targeted
015 reuse, 011 generic inspector, compatibility, and 022 revocation cases.
The final unit runs the full suite. Product acceptance remains pending for
implementation; no product tests were run during planning.
