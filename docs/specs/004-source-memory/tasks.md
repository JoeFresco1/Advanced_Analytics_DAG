# Tasks: 004 â€” Slice C live second inquiry using source memory

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001-t002

- [ ] T001 Create the local SQLite source variants, fixture principal/grants, stable source/version IDs, and independent inquiry inputs in `src/analytics/demo-source-memory.ts`.
- [ ] T002 Add a demo driver in `src/analytics/demo-source-memory.ts` that starts `dag analytics ask` with the synthetic source and `gpt-6-luna`/`xhigh`, captures inquiry and evidence refs, and opens the existing answer/trace path without writing committed records itself.

## scope-t003

- [ ] T003 Drive the first ordinary `dag analytics ask` through the registered 014 `orient` hook in `src/analytics/demo-source-memory.ts`; assert the hook uses the same grant- and budget-bounded discovery implementation as 014 `source discover`, commits a candidate, and returns only its safe reference to orientation. Independently replay a bounded recipe, then use 003's normal typed command registry to invoke 022's registered `skills review` command with the exact candidate and configured owner. Add the runnable `004 first ordinary ask` acceptance case in `src/analytics/acceptance/004.test.ts`. Assert only validated, conflict-resolved owner approval can activate; retain failed/missing replay, unknown meaning, or conflicting definitions as `candidate` without retrying until favorable or using a seeded-active shortcut.

## scope-t004

- [ ] T004 After 022 commits activation, start a second ordinary independent `dag analytics ask` through 015's registered source-reuse `orient` hook in `src/analytics/demo-source-memory.ts`; assert it receives the exact active source/skill refs and records supplied references separately from executed recipes. Add the runnable `004 second ordinary ask` acceptance case in `src/analytics/acceptance/004.test.ts`; inspect the answer and claim with `answer show --inquiry <id> --open` and `trace --claim <id>`, then request 011's existing `GET /api/analytics/inquiries/{inquiryId}/answer` and `GET /api/analytics/claims/{claimId}/trace` APIs. Assert their generic linked-provenance projection exposes committed source, skill/status, validation, authority, supplied-reference, executed-recipe, and exposure refs with exact revisions/digests under current grants. Add no special dispatcher, 004 viewer route, or feature-specific DTO.

## scope-t005

- [ ] T005 Add the runnable `004 compatibility` acceptance case in `src/analytics/acceptance/004.test.ts` and exercise 015's single compatibility predicate against same-schema semantic, tenant, key/grain, unit, timezone/category, role/grant, stale-watermark, and later-window join changes; preserve unrelated guidance for an unused-column change and preserve each inquiry's locked input.

## scope-t006

- [ ] T006 Add the runnable `004 revocation` acceptance case in `src/analytics/acceptance/004.test.ts`; invoke 022's registered `skills revoke` command with the configured owner, exact active version, withdrawal reason, and expected project revision, then verify earlier inquiries retain their original skill pin while later orientation receives zero current guidance from the revoked version and does not substitute a refreshed source or skill.

## trust-and-final-acceptance

- [ ] T007 Add bounded-stop, untrusted-text, grant-scope, sensitive-content, and prior-outcome cases to `src/analytics/acceptance/004.test.ts`; assert no out-of-scope recipe executes, no protected values enter the package, and no outcome enters later context without an exposure record.
- [ ] T008 Run `pnpm test` for feature 004 and write actual command results, test evidence for every FR/SC/ordered Edge Case, source and skill provenance visible in the inspector, and any accurate local access-boundary limits to `specs/004-source-memory/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001-t002 | Completed feature prerequisites |
| scope-t003 | scope-t001-t002 |
| scope-t004 | scope-t003 |
| scope-t005 | scope-t004 |
| scope-t006 | scope-t005 |
| trust-and-final-acceptance | scope-t006 |
