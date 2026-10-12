# Tasks: 019 â€” Artifact catalog, bounded exports, notebooks and legacy import

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t001

- [ ] T001 Define validated `ArtifactRecordV1`, `CatalogEntryV1`, role/retention/availability fields, stable opaque IDs, digest rules, source-class distinction, and usage-link types in `src/analytics/catalog.ts`. Keep committed, staged, failed and imported material distinguishable; imported origin defaults to explicit unknown. Do not add a persistence/CAS implementation.

## bounded-import

- [ ] T003 Implement approved-root path handling, bounded CSV/XLSX parsing and content reads under current sheet grants. Enforce 100 files/1 GiB/300 seconds before additional reads; reject traversal, unsafe workbooks and denied title/sheet/cell disclosure. Test the 99-file corpus, 101st-file/byte/time stops and metadata-only sheets. Never move originals.
  - Files: `src/analytics/import.ts`, `src/analytics/catalog.ts`, `src/analytics/acceptance/019.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## import-history

- [ ] T010 Use the bounded importer to commit immutable path-specific source/import versions through 009. Group byte-identical content without merging distinct provenance; origin remains unknown despite a final filename. On rescan append change events and reassessment_required projections; missing input becomes unavailable without rewriting history. Test changed/missing input and duplicate groups.
  - Files: `src/analytics/import.ts`, `src/analytics/catalog.ts`, `src/analytics/acceptance/019.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## committed-index

- [ ] T004 Define and validate CatalogProjectionInputV1 and ingest only committed reachable artifact/import/card and typed ledger projections. Implement idempotent FTS5 rebuild from recovered 009 run/project manifest chains after deletion/corruption. Preserve exact record/commit refs, scopes, availability, validity and usage. Do not import future 023 or infer ledger semantics.
  - Files: `src/analytics/import.ts`, `src/analytics/catalog.ts`, `src/analytics/acceptance/019.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## curation-events

- [ ] T011 Implement appendCurationEventV1 with current project-owner grant, stable actor/target/action/scope/evidence/commit bindings and durable 009 project writes. Rebuild the index from a committed curation event after corruption without changing canonical source bytes or history; an index edit is never authority.
  - Files: `src/analytics/import.ts`, `src/analytics/catalog.ts`, `src/analytics/acceptance/019.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## access-first-search

- [ ] T012 Implement access-first FTS5 lookup/ranking/context construction and durable recordDeniedLookupV1. Reject restricted workbook metadata before matching/ranking; persist only safe principal/opaque source/operation/reason. Test zero title/path/sheet/cell/rationale/count disclosure and retained ledger/card/artifact refs. Keep proposal T39 primary ownership with 020.
  - Files: `src/analytics/import.ts`, `src/analytics/catalog.ts`, `src/analytics/acceptance/019.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## scope-t002

- [ ] T002 [US1] Register and reconcile committed run outputs from node-009 manifests in `src/analytics/catalog.ts`; persist required project catalog/curation references only through `commitProjectMemory` and rebuild after `recoverProjectMemory`. Export `analyticsExtensionV1` with the typed `analytics catalog search` and `analytics catalog rebuild` command handlers and the `afterEvidenceCommit(context, manifestRef)` lifecycle hook, using the real node-003 registry/dispatcher contracts without editing 003-owned files. Add `src/analytics/acceptance/019.test.ts` fixtures that invoke the registered catalog commands and hook through the node-003 caller path, checking typed/access-safe `CommandResultV1` payloads, durable 009 recovery, hook replay, stable ID/digest/role/producer/availability/scope/usage bindings, committed-versus-staged state, post-run-commit catalog failure with completion withheld until hook recovery, idempotent rebuild, and forbidden answer-story inference.

## scope-t009

- [ ] T009 [US2] Implement and register the typed `analytics artifacts import` command handler in `src/analytics/catalog.ts`, delegating bounded CSV/XLSX work to `src/analytics/import.ts`. Bind the invocation to the authenticated principal, project/source refs and current metadata/sheet grants supplied through the node-003 command context; never accept a caller path or worker-supplied grant as authority. Return only bounded, access-safe import status and committed refs from node-009 project memory. Add `src/analytics/acceptance/019.test.ts` coverage through the real node-003 registry/typed dispatcher for the 99-file import, count/byte/time rejection before further reads, mixed workbook sheet grants, zero unauthorized title/sheet/cell disclosure, unknown-origin/path-specific identities, and committed imported-versus-result separation. This handler-path workbook denial is supporting coverage for 020-owned proposal T39; 019 does not own T39.

## scope-t005

- [ ] T005 [US3] Implement controller output declaration and bounded publication in `src/analytics/retention.ts` and register the typed `analytics artifacts export` handler in `src/analytics/catalog.ts`: require a declared canonical role, retention class, producer, approved root and count/byte limits; test a 100-output profile and reject the 101st or byte overflow before publication. Enforce zero default CSV/XLSX exports, retain results on rejection, flag observed unmanaged writes without claiming enforcement, and bind one authorized export to its exact committed Result and answer-card versions in `src/analytics/acceptance/019.test.ts`.

## scope-t006

- [ ] T006 [US3] Implement preview/apply and interruption recovery for retention in `src/analytics/retention.ts`, registering typed retention command handlers in `src/analytics/catalog.ts` and persisting cleanup intent/completions through node-009 project memory. Delete only enumerated managed scratch or eligible cache with no retained dependency. Add `src/analytics/acceptance/019.test.ts` fixtures for interrupted sweep recovery twice, no evidence/user/import/out-of-root deletion, preserved answer trace, and unchanged final managed-file inventory.

## scope-t007

- [ ] T007 [US4] Implement optional notebook publication/replay validation in `src/analytics/retention.ts` and register the typed `analytics notebook publish` handler and artifact in `src/analytics/catalog.ts`. Use the existing node-009 stage adapter; bind input versions, Python/dependency/environment/program digests, ordered cell IDs/digests, fresh-run outcome and every displayed Claim to committed Result IDs. Keep unsupported or hidden-state notebooks exploratory. Add clean replay, order/environment pinning, missing Result link, hidden-state and failed-replay fixtures in `src/analytics/acceptance/019.test.ts`.

## scope-t008

- [ ] T008 Complete all synthetic/local 019 fixtures in `src/analytics/acceptance/019.test.ts` for every FR, SC and ordered edge case plus 019-owned proposal cases T28–T30, T37 and T43–T47. Also retain `f019-t39-restricted-workbook-denial` as supporting 019 catalog/access-boundary coverage while 020 owns primary proposal T39 acceptance. Exercise the final `analyticsExtensionV1` through node-003's actual registry/typed command dispatcher for catalog, import, export, retention and notebook commands, and deliver `afterEvidenceCommit` through its lifecycle dispatcher with injected failure and idempotent recovery; assert the required hook failure leaves completion unacknowledged until recovery succeeds. Include deletion/corruption rebuild from versioned synthetic committed ledger attempt, rationale, match-assessment, answer-card, artifact and curation records; assert unchanged canonical source bytes/history and preserved IDs, access scopes, availability, current validity, usage links and access-safe rationale retrieval. Run the focused 019 suite and full `pnpm test`; write `specs/019-catalog-analytical-artifacts/verification.md` with actual command results and concrete artifact/evidence references for FR-001–FR-017, SC-001–SC-017 and EC-001–EC-007. Preserve null, negative, failed and incomplete states; do not edit accepted requirements, answer keys or thresholds.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t001 | Completed feature prerequisites |
| bounded-import | scope-t001 |
| import-history | bounded-import |
| committed-index | import-history |
| curation-events | committed-index |
| access-first-search | curation-events |
| scope-t002 | access-first-search |
| scope-t009 | scope-t002 |
| scope-t005 | scope-t009 |
| scope-t006 | scope-t005 |
| scope-t007 | scope-t006 |
| scope-t008 | scope-t007 |

## Canonical decomposition: retention-recovery

- [ ] T013 Complete original T006 interruption recovery: resume only the enumerated durable cleanup intent, stable action IDs and per-file completions. Reject root/grant/target drift; preserve every canonical/raw/final/unmanaged file. Verify failure after one deletion and replay through real project-memory recovery.
