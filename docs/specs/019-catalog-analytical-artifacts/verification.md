# Verification Map: Catalog and Manage Analytical Artifacts

## Planning-stage status

This file records the planned verification path for feature 019. Product implementation and acceptance commands have not run in this task-plan-repair stage. Final task T008 replaces the planned evidence references below with actual command outcomes and committed artifact/evidence refs; it must leave every checklist task unchecked during planning.

The frozen verification maps identify the primary success criterion for each FR. `execution.json` maps every FR, SC and ordered edge case to real checklist tasks. The fixture names below are planned evidence targets, not claims that those fixtures already exist or pass.

## Functional requirement coverage

| Requirement | Primary SC | Task owner(s) | Planned evidence |
| --- | --- | --- | --- |
| FR-001 | SC-014 | T001, T002, T008 | Stable artifact ID/digest/media/role/class/producer/availability/scope/usage assertions in committed-manifest inventory fixture. |
| FR-002 | SC-014 | T002, T008 | Committed, staged, failed and imported source-class cases; staged/partial outputs stay non-authoritative after recovery. |
| FR-003 | SC-016 | T003, T009, T008 | 99-file corpus plus 101-file, byte-overflow and elapsed-time stop cases through the typed import caller. |
| FR-004 | SC-001 | T003, T008 | Legacy corpus with misleading `final` filename and explicit unknown origin. |
| FR-005 | SC-002 | T003, T008 | Identical bytes at distinct paths retain distinct artifact IDs and path identities while grouping by digest. |
| FR-006 | SC-011 | T003, T008 | Changed/missing rescan creates a new version or unavailable state and preserves prior provenance. |
| FR-007 | SC-008 | T003, T004, T009, T008 | Restricted title/row fixture proves grant filtering before FTS and context construction; no protected metadata in responses or committed denial event. |
| FR-008 | SC-003 | T002, T004, T008 | Post-commit catalog/index failure and recovered idempotent registration with no duplicates or staged promotion. |
| FR-009 | SC-015 | T005, T008 | Declared role/class/count/byte admission rejects invalid or over-limit publication before visibility. |
| FR-010 | SC-004 | T005, T008 | 1,000 default CSV/XLSX export requests publish zero artifacts and preserve structured results. |
| FR-011 | SC-004 | T005, T008 | One authorized export resolves to its exact committed Result and answer-card revisions; Result remains independently accessible. |
| FR-012 | SC-005 | T006, T008 | Cleanup fixture preserves evidence dependencies, imports, user paths and files outside managed roots. |
| FR-013 | SC-017 | T007, T008 | Notebook replay pins inputs, environment and ordered cells; every displayed Claim binds to a Result; hidden-state/failure stays exploratory. |
| FR-014 | SC-007 | T005, T008 | Unrestricted-harness fixture reports observed unmanaged output without claiming write-root enforcement. |
| FR-015 | SC-015 | T003, T009, T008 | Mixed-grant workbook dispatched through the import handler; metadata-only reveals no cells and denied metadata reveals no sheet name. |
| FR-016 | SC-010 | T006, T008 | Interrupted retention intent resumes twice to identical managed inventory without evidence or user-file deletion. |
| FR-017 | SC-012 | T004, T008 | Deleted/corrupt index rebuilds from committed 009 manifests/import/curation records with stable IDs, scope, availability and usage refs. |

## Success criterion coverage

| Criterion | Task owner(s) | Planned evidence |
| --- | --- | --- |
| SC-001 | T003, T008 | `f019-t29-legacy-corpus`; no filename-derived story or invented execution provenance. |
| SC-002 | T003, T008 | `f019-t30-path-preserving-dedup`; every original path remains separately identifiable. |
| SC-003 | T002, T004, T008 | `f019-t28-committed-output-rebuild`; interrupted registration recovers each committed output exactly once. |
| SC-004 | T005, T008 | `f019-t43-export-bound` and `f019-t46-result-bound-export`; zero default exports and one exact Result-bound workbook. |
| SC-005 | T006, T008 | `f019-t44-retention-cleanup`; evidence trace survives and no ineligible path is deleted. |
| SC-006 | T007, T008 | `f019-t45-notebook-replay`; clean replay is linked, hidden-state replay remains exploratory. |
| SC-007 | T005, T008 | `f019-t47-unmanaged-write-warning`; report observed write without an enforcement claim. |
| SC-008 | T003, T004, T009, T008 | Restricted title/row denial in import and catalog caller paths, with no search/context disclosure. |
| SC-009 | T003, T009, T008 | Mixed-permission workbook imports only authorized sheets and profiles zero unauthorized cells. |
| SC-010 | T006, T008 | Two idempotent recoveries yield identical managed-file inventory. |
| SC-011 | T003, T008 | Changed and removed source records preserve historical identity and show current change/unavailability. |
| SC-012 | T004, T008 | `f019-t37-index-rebuild`; committed refs and scopes survive index loss; staged files remain absent. |
| SC-013 | T003, T009, T008 | 99 files finish within bounds; file 101 is not read or recorded as inspected. |
| SC-014 | T001, T002, T008 | Committed output record exposes the required identity, digest, media, role/class, producer, availability, scope and usage links. |
| SC-015 | T003, T005, T009, T008 | Output overflow is rejected before publication; workbook metadata/cell disclosure follows exact grants. |
| SC-016 | T003, T009, T008 | Byte/time limits stop before further reads; uninspected files gain no import record; larger run requires a distinct authorized profile. |
| SC-017 | T007, T008 | Notebook record pins all required replay metadata and Result IDs; any missing pin leaves it exploratory. |

## Ordered edge-case coverage

| Edge | Accepted edge case | Task owner(s) | Planned evidence |
| --- | --- | --- | --- |
| EC-001 | Two paths contain identical bytes but represent separate user-visible files. | T003 | Distinct path IDs remain while digest grouping is access-filtered. |
| EC-002 | An Excel workbook has several sheets; parsing some sheets is not permitted. | T003, T009 | Mixed-grant workbook through parser and typed import command; denied sheets are not parsed or disclosed. |
| EC-003 | A filename says “final” but there is no evidence it was used. | T003, T004 | Unknown-origin import remains an inventory entry and does not become a Result, Claim or answer. |
| EC-004 | A catalog update fails after evidence was committed. | T002, T004, T008 | Required lifecycle hook failure withholds completion acknowledgement until idempotent recovery. |
| EC-005 | A file contains sensitive values that must not enter a search index. | T003, T004, T009 | Verify zero protected values in profile, FTS, response, committed denial event and worker context. |
| EC-006 | A local harness can write outside declared paths despite output contracts. | T005 | Surface observed unmanaged file without claiming prevention. |
| EC-007 | Cleanup is interrupted after deleting some eligible managed scratch files. | T006 | Resume the same durable intent twice and compare final inventory; retain evidence/user files. |

## Proposal-case ownership and supplemental coverage

| Proposal case | 019 verification role |
| --- | --- |
| T28 | Primary 019 case: T002 / `f019-t28-committed-output-rebuild`. |
| T29 | Primary 019 case: T003 / `f019-t29-legacy-corpus`. |
| T30 | Primary 019 case: T003 / `f019-t30-path-preserving-dedup`. |
| T37 | Primary 019 case: T004 / `f019-t37-index-rebuild`. |
| T39 | Supporting 019 catalog denial fixture: T004 / `f019-t39-restricted-workbook-denial`. Feature 020 owns primary T39 acceptance, per `docs/specs/traceability-audit.md` and `docs/build/acceptance-plan.md`. |
| T43 | Primary 019 case: T005 / `f019-t43-export-bound`. |
| T44 | Primary 019 case: T006 / `f019-t44-retention-cleanup`. |
| T45 | Primary 019 case: T007 / `f019-t45-notebook-replay`. |
| T46 | Primary 019 case: T005 / `f019-t46-result-bound-export`. |
| T47 | Primary 019 case: T005 / `f019-t47-unmanaged-write-warning`. |

The T39 supporting fixture verifies 019's own rule that current access is checked before catalog FTS matching/ranking and worker-context construction. It does not replace or reassign 020's primary proposal-case check.

## Frozen interface and execution checks

| Boundary | Planned verification |
| --- | --- |
| Node 003 extension | `catalog.ts` exports `analyticsExtensionV1`; T002/T005/T006/T007/T009 dispatch typed commands and lifecycle hook through the real registry/dispatcher. No 003-owned registry, CLI or controller files are modified. |
| Import handler ownership | T009 explicitly owns `analytics artifacts import` registration in `catalog.ts`; import logic remains in `import.ts`; authenticated context supplies principal, project/source refs and grants. |
| Node 009 persistence | Imports, curation, denied lookups and retention intents use `commitProjectMemory`/`recoverProjectMemory`; stable commit IDs make recovery idempotent. No second CAS or persistence engine is introduced. |
| Catalog authority | Committed run/project manifest chains and durable curation records are authoritative; SQLite FTS5 is rebuildable only and cannot promote staged output. |
| Access and disclosure | Principal/current grants are checked before reads and before ranking/context; safe denial payloads withhold restricted title/path/sheet/cell metadata. |
| Build-owned files | Execution units own every output listed for feature 019: `catalog.ts`, `import.ts`, `retention.ts`, `acceptance/019.test.ts` and this `verification.md`. |

## Commands and evidence status

The implementation units declare focused `pnpm typecheck` and `pnpm exec tsx --test src/analytics/acceptance/019.test.ts` checks, with final `pnpm test`. Those product commands remain pending until implementation. Planning validation passed with `node scripts/build/check-plan.mjs 019`: 9 checklist tasks, 4 executable units, and no product-code edits. This guard checks task/manifest structure and planning-only file scope, not product behavior.
