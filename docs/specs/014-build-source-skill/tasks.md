# Tasks: 014 â€” Source discovery generates a validated skill package

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001

- [ ] T001 Define source identity, current grant scope, provenance types, fixed discovery bounds, and the read-only SQLite bridge in src/analytics/source-discovery.ts. Resolve an opaque registered source only after its metadata grant is checked; use executeStageV1, a read-only URI, query-only mode, a transaction-bound window, and an immutable source version or explicit unknown value.

## scope-t002

- [ ] T002 [US1] Implement bounded schema and aggregate discovery in src/analytics/source-discovery.ts, including table/column/profile/sample caps, source-version and snapshot limitations, and safe stop reasons.

## scope-t003

- [ ] T003 [US1] Generate immutable SKILL.md, manifest, scoped schema/definition/grain/time references, provenance, access metadata, and candidate lifecycle records in src/analytics/source-skills.ts; publish through 009 commitProjectMemory without a second CAS or registry.

## scope-t004

- [ ] T004 [US1] Generate and execute parameterized Python aggregate examples under the intended grant through the existing stage service in src/analytics/source-discovery.ts, bind each tested assertion to its source version/window and execution evidence, and add source-core acceptance cases in src/analytics/acceptance/014.test.ts. Using a fixture with known keys, grain, joins, definitions, and time columns, compare each generated guidance claim with the fixture truth at the named source version/window; fail on invented, mismatched, or omitted required guidance. Separately test an unknown definition and two owner-conflicting definitions: preserve the named authority and both evidence references, mark the claims unresolved/inactive, and never promote either interpretation. Also assert bounded read-only discovery, executed recipes, provenance, and inspectable candidate contents. The source-core unit runs the entire 014.test.ts file at this stage, so its assertions cannot be skipped by a name filter.

## scope-t005-t007

- [ ] T005 [US1] Export analyticsExtensionV1 with the frozen source discover command and production orient(context) hook from src/analytics/source-discovery.ts. For ordinary ask, read source-skill refs from the committed 009 project head and apply 001 filterAccessible before checking active status for the exact source identity, environment, and tenant. With zero matching active refs, require current metadata and aggregate grants plus remaining discovery budget, then call the same bounded discovery implementation as source discover and return only safe candidate refs/scope. With one or more matching active refs, return safe refs only and perform no discovery or compatibility assessment; defer that judgment to the later 015 orientation hook. Stale/revoked records do not count as active, but do not infer revoked authority or bypass current access. Do not call or consume any 003/015 compatibility service, add a shared 003 context field, or supply candidate content as active guidance.
- [ ] T007 [US1] Add acceptance cases that invoke the registered `source discover` command through the 003 extension registry, verify the command reaches the bounded discovery implementation, commits an inspectable candidate, and reports safe refs/scope; also verify a denied grant prevents a source read or commit. Separately add production orient(context) acceptance cases in src/analytics/acceptance/014.test.ts. Prove zero accessible active refs for the exact source identity/environment/tenant trigger the shared cold-discovery implementation, commit only a candidate, and return safe refs/scope without content; prove one or more matching active refs return safe refs only with no rediscovery, candidate content, compatibility judgment, or 003/015 compatibility-service call. Include sibling-scope, access-denied, uncommitted/staging, and stale/revoked refs to verify only accessible committed exact-scope active records suppress cold discovery; prove 019 is not called for source-skill lookup. Assert unresolved/owner-conflicting definition claims and their authority/evidence refs remain inactive in the returned orientation metadata.

## scope-t006-t008

- [ ] T006 [US2] Enforce exact source-audience filtering and inert treatment of comments, labels, stored SQL, and examples in src/analytics/source-skills.ts; exclude credentials, raw rows, and all study-specific outcome summaries.
- [ ] T008 [US2] Add contaminated-source, outcome-content, audience-filtering, and unsafe-recipe cases in src/analytics/acceptance/014.test.ts, plus cold-orientation cases with missing/expired metadata or aggregate grants and exhausted discovery budget. Assert source text cannot change privileged commands, access, checks, or policy; sensitive metadata, credentials, raw rows, and untagged study outcomes do not leak; unbounded or over-grant recipes do not run for the weaker audience; and denied/budget-exhausted orientation performs no source read or candidate commit.

## acceptance-and-verification

- [ ] T009 Run the Task 014 acceptance suite and write FR/SC/edge evidence, actual command results, and any honest local-access/version limits to specs/014-build-source-skill/verification.md.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001 | Completed feature prerequisites |
| scope-t002 | scope-t001 |
| scope-t003 | scope-t002 |
| scope-t004 | scope-t003 |
| scope-t005-t007 | scope-t004 |
| scope-t006-t008 | scope-t005-t007 |
| acceptance-and-verification | scope-t006-t008 |
