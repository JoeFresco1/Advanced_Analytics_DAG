# Task 014 Verification Plan

**Status**: Planned only. No product files have been implemented and no acceptance test has run. The final acceptance unit T009 must replace this plan with actual commands, results, and evidence references.

## Acceptance commands

- **Source-core unit**: `pnpm build`, then `node --test dist/analytics/acceptance/014.test.js`. At this stage only source-core tests have been added; the entire file runs, so a missing name match cannot produce an empty pass. The fixture checks each key, grain, join, definition, and time guidance claim against known source truth at the named version/window, and checks unknown/owner-conflicting definitions retain authority plus both evidence references and remain inactive. It also covers bounded read-only discovery, executed parameterized Python examples, source assertion provenance, and candidate package inspection before the orientation work is complete.
- **Registry-and-trust unit**: `pnpm build`, then `node --test dist/analytics/acceptance/014.test.js`. The registry fixtures invoke the registered `source discover` command and prove candidate publication plus a denied-grant stop. The production-hook fixtures cover both exact-scope active-reference branches plus access and contaminated-source failures; these checks run before the final feature unit.
- **Final feature unit**: `pnpm test`.
- **Fixture**: synthetic local SQLite databases created by `src/analytics/acceptance/014.test.ts`; no remote or confidential source is used.

## Production orientation acceptance

First invoke the registered `source discover` command through the 003 extension registry against the synthetic source: assert bounded discovery runs, an inspectable candidate is committed, safe refs/scope are returned, and a denied grant prevents source read or commit. Then invoke the exported production `orient(context)` hook directly, separately from the command.

1. With zero currently accessible active source-skill refs for the exact source identity, environment, and tenant, prove the hook checks metadata/aggregate grants and remaining budget, calls the same bounded discovery implementation as `source discover`, commits only an immutable candidate, and returns safe candidate refs/scope without SKILL.md content in active guidance.
2. With one or more currently accessible active refs for that exact scope, prove the hook returns safe refs only, performs no source discovery or candidate commit, supplies no candidate content, and makes no compatibility judgment or 003/015 compatibility-service call. The later 015 orientation hook owns the compatibility predicate.
3. Prove refs for another environment or tenant, inaccessible active refs, and uncommitted/staging refs do not suppress cold discovery; the lookup reads committed 009 project-manifest skill refs through 014-owned projection and 001 filterAccessible, without a 019 source-skill lookup. Stale/revoked records do not count as active; their absence does not allow 014 to infer revoked authority or bypass current grant checks.
4. With a missing/expired metadata grant, missing/expired aggregate grant, or exhausted discovery budget on the zero-active path, prove there is no adapter invocation and no project-memory commit.

## Functional requirement evidence

| Requirement | Task(s) | Planned evidence |
| --- | --- | --- |
| FR-001 | T001, T003, T005, T007 | Candidate manifest and refs plus safe orientation scope bind exact source identity, environment, role, audience, schema/meaning scope, tenant, and applicable version. |
| FR-002 | T003, T004, T007 | Package contains short SKILL.md, four scoped references, and a bounded example whose execution is recorded; orientation returns refs/scope only. |
| FR-003 | T001, T003, T004 | Every assertion records declared/observed/tested-on-version/inferred provenance; tested assertions bind the source version/window and execution evidence. |
| FR-004 | T004, T007, T008, T009 | Admitted recipes execute with their intended grant before being recorded as tested; untested or over-grant recipes do not run under the weaker audience. |
| FR-005 | T006, T008 | Contaminated comments/examples remain inert and cannot alter access, checks, commands, or policies. |
| FR-006 | T003, T006, T008 | Candidate package inspection finds no credentials, raw sensitive rows, or untagged study-specific outcomes. The lifecycle remains `candidate`; 014 does not claim activation. |
| FR-007 | T003, T007, T008 | Unknown and owner-conflicting meanings remain explicit with authority and both evidence refs; no assertion is activated and the package stays `candidate`. |
| FR-008 | T001, T006, T008 | A narrower role cannot receive table/column metadata from a candidate scoped to a more privileged audience. |

## Success criterion evidence

| Criterion | Task(s) | Planned evidence |
| --- | --- | --- |
| SC-001 | T004, T007 | For each fixture key, grain, join, definition, and time claim, compare generated guidance to fixture truth at the intended source version/window; mismatch, invention, and omitted required guidance fail the test. |
| SC-002 | T006, T008 | Injection fixture proves policy/access/worker instructions are unchanged and instruction-like comments are omitted from generated guidance. |
| SC-003 | T006, T008 | Inspect the complete persisted candidate package for zero credentials, raw sensitive rows, and untagged study outcomes before any downstream activation. Activation itself remains outside 014. |
| SC-004 | T003, T004, T007, T008 | Test unknown and two owner-conflicting definitions: neither interpretation activates; uncertainty/conflict, named authority, and both evidence refs remain inspectable in the candidate and inactive in orientation. |
| SC-005 | T006, T008 | Narrow-role lookup reveals zero privileged table or column metadata. |
| SC-006 | T003, T004, T007 | Package inspection checks all scope fields, entry guidance, on-demand refs, bounded tested recipe, and assertion provenance; the production orient hook is invoked independently and returns reference-only candidate metadata on its cold path. |
| SC-007 | T006, T008 | Unbounded and over-grant recipes remain candidate, are not executed for a weaker audience, and are not surfaced as active guidance. |

## Ordered edge-case evidence

| Edge case | Task map | Planned fixture assertion |
| --- | --- | --- |
| EC-001: Identical table names differ by environment | T007 | Distinct source/environment scopes produce separate packages and meaning claims; refs from another environment do not suppress cold discovery. |
| EC-002: Unknown or owner-conflicting source definition | T003, T004, T007, T008 | Fixture asserts unknown remains unknown; both conflicting interpretations and evidence refs retain named authority and neither activates. |
| EC-003: Unbounded or over-privileged recipe | T004, T008 | Recipe is not run under the weaker grant and remains a candidate limitation. |
| EC-004: Metadata is sensitive to a broader audience | T006, T008 | Narrow-role lookup reveals no privileged table or column metadata. |

## Proposal-case evidence

| Case | Task map | Planned fixture assertion |
| --- | --- | --- |
| T17: Schema comment or source text contains instructions | T006, T008 | Instruction-like text is omitted from generated guidance and cannot change access, policy, checks, or worker instructions. |
| T25: Source skill or shared summary contains evaluation findings | T006, T008 | Study-specific outcome content is excluded from the candidate package. No finding is loaded as guidance or presented without outcome provenance; activation and later-use exposure remain outside this feature. |

## Limits to record during implementation

Record whether the host actually enforces the source read boundary, whether source-byte/query-cost telemetry is available, and whether the SQLite source had an immutable version or repeatable snapshot. Unknown guarantees remain unknown or workflow-only. The package remains a candidate; activation and reuse belong to later features.
