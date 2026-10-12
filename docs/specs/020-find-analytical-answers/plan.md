# Implementation Plan: Find Past Analytical Answers

**Branch**: `020-find-analytical-answers` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/020-find-analytical-answers/spec.md`

## Summary

Implement a durable answer-card path for every completed or stopped inquiry, a deterministic evidence-only fallback when worker prose is missing, append-only curation, access-first search and ranking, and automatic recall on a later inquiry. `answers.ts` owns answer/fallback and curation records; `retrieval.ts` exports `analyticsExtensionV1`, consumes the existing 019 accessible catalog and 023 orientation handoff, and writes retrieval/exposure records through 009 project-memory commits. The search evaluation remains the frozen 150-card/20-query benchmark with Hit@3 ≥ 0.90 without pins and five no-match queries with zero false direct answers. The final path includes the 99-file story lookup and a later live worker recall with no user hint.

## Technical Context

**Language/Version**: TypeScript, Node.js ≥20; use the workspace's existing strict TypeScript configuration.

**Primary Dependencies**: Existing 003 `AnalyticsExtensionV1` registry and lifecycle hooks; 009 `commitProjectMemory`/`recoverProjectMemory`; 019 access-filtered SQLite FTS5 catalog; 023 `AnalyticalMatchV1` orientation assessment; 011 answer and evidence-trace viewer. No new package or shared CLI/controller code.

**Storage**: Versioned project-memory records committed by 009. The 019 SQLite/FTS5 catalog remains a rebuildable projection; 020 does not create a second store or index.

**Testing**: `pnpm build` and `pnpm test`, including synthetic/local cases in `src/analytics/acceptance/020.test.ts`. Final feature acceptance also records the required `gpt-6-luna` / `xhigh` live recall demonstration through the already-owned ask path.

**Target Platform**: Local analytics project under the current TypeScript/Node toolchain.

**Project Type**: Internal TypeScript analytics controller, feature provider, and acceptance fixture.

**Performance Goals**: For the frozen 150-card and 20-query fixture, place the primary relevant card in the first three for at least 18 of 20 queries, measured as qualifying queries divided by 20, with pins disabled. Each of five no-match queries, including the empty corpus, returns zero false direct answers. The 99-file fixture remains searchable by question and period without filename knowledge.

**Constraints**: Apply access filtering before candidate ranking or context construction. A card is `direct_answer` only when intent and metric match and known population, period, source meaning, evidence, and current validity are compatible. Unknown compatibility cannot become a direct answer. `related` and `not_relevant` retain attributed reasons. Pins and recency break ties only. Committed history and curation remain append-only; fallback text uses only committed structured facts. Existing schemas, accepted requirements, fixtures, relevance labels, thresholds, 003/009/019/023 files, `dag.run.json`, and shared controller/CLI files remain unchanged.

**Scale/Scope**: One project-scoped answer library, a bounded orientation context, the frozen 150-card/20-query ranking fixture, five no-match queries, and the 99-file CSV/workbook inquiry story. No cross-project discovery or vector search is part of this slice.

## Constitution Check

| Gate | Design response | Result |
| --- | --- | --- |
| Honest answers and separate execution/evidence states | Persist negative, inconclusive, failed, stopped, and incomplete work with its actual evidence state. Fallback prose does not infer a conclusion. | Pass |
| Append-only history and exposure | Use stable immutable answer, curation, retrieval, and use/disposition records committed by 009; record supplied outcome exposure. | Pass |
| Scoped and recoverable project memory | 019 filters accessible catalog candidates; 009 commits records; 019 rebuilds the search projection without losing IDs, pins, scope, availability, or validity. | Pass |
| Bounded and accurate guarantees | Limit orientation context; make only the workflow access guarantee supported by the local fixture. | Pass |
| Producer, consumer, inspectable result, and failure path | 003 calls the provider hooks, 019/023/009 supply owned services, 011 exposes answer/trace links, and fixtures cover omitted prose, access denial, stale evidence, and no-match. | Pass |
| Preserve approved feature contracts | The 150/20, Hit@3, no-match, 99-file, and live-recall criteria are held fixed; no checklist or policy edits. | Pass |

No exception or complexity violation is required. The pre-design and post-design gates both pass.

## Production Path and Ownership

- **Producer**: 003 inquiry finalization and orientation invoke 020's feature-owned lifecycle hooks. Worker story selection remains attributed to the worker; deterministic code verifies record links, state values, access, and ranking rules.
- **Durable consumer**: `answers.ts` builds a card or fallback from committed inquiry records and calls the 009 project-memory commit/recovery interfaces. Curation and retrieval disposition are immutable events.
- **Catalog consumer**: `retrieval.ts` obtains candidates only through 019's current-access-filtered index/search surface. It never reads SQLite as an authority and never repeats catalog recovery logic.
- **Ledger handoff**: 023's `orient` hook assesses prior attempts once and places its `AnalyticalMatchV1` handoff before 020's `orient` hook. 020 consumes this result read-only and does not call the 023 match operation again. 020 then ranks answer cards.
- **User inspection**: `answers search` returns typed card refs, classification reasons, scope, validity, evidence links, and ledger-timeline links. The feature-owned `answers home` view lists recent, pinned, and unresolved accessible cards without a query. Existing 011 answer/trace routes display card and timeline references; 020 does not edit 011 viewer/server files.
- **Failure and recovery**: Commit a deterministic fallback only from committed state; use stable IDs/commit IDs so hook replay is idempotent. A failed required hook cannot acknowledge a completed inquiry. Redaction/revocation hides protected metadata while retaining a safe historical pointer.
- **Compatibility**: Implement only `src/analytics/answers.ts`, `src/analytics/retrieval.ts`, and `src/analytics/acceptance/020.test.ts` as product outputs. Do not change `src/analytics/extensions.ts`, `controller.ts`, `cli.ts`, 009 store/publication, 019 catalog/index, 023 ledger, 011 HTTP/viewer, shared build documents, package manifests, or `dag.run.json`.

## Design Decisions

1. **Keep the existing extension handoff authoritative.** `retrieval.ts` exports the frozen `analyticsExtensionV1`, depends on `catalog` and `ledger`, and runs after 023. It ranks answer cards from the 019 filtered candidate set. Its orientation input includes the earlier 023 assessment; no call to 023 matching occurs inside 020. Acceptance asserts one match call and one retrieval event.
2. **Keep committed records authoritative.** `answers.ts` writes cards and curation events through 009. 019 indexes committed cards and events idempotently. Index loss is repaired from committed run/project memory. No second SQLite schema is authoritative.
3. **Finalize from committed facts.** Worker-selected story fields carry a selection reason and exact Claim/Result refs. If the worker summary is absent, deterministic fallback uses the original question, canonical inquiry/evidence states, scope, committed refs, and a missing-interpretation/incomplete label; it never writes a finding that is not present in committed evidence.
4. **Append curation, do not mutate Claims.** Pin, title/annotation, correction, and supersession decisions are append-only curation events. An explicit “remember this analysis” instruction at close creates a pin event for the finalized scoped card. It does not create a skill or alter Claim support, scope, access, or validity.
5. **Rank only after access filtering.** Obtain accessible candidate IDs from 019 before computing lexical or structured relevance. Known structured scope fields determine compatibility; worker-authored intent/meaning rationale stays attributed. Missing required compatibility can be `related` at most. Sort direct answers ahead of related cards; within an equal relevance tier apply deterministic structured/lexical relevance, then optional pin/recency tie-breaks, then stable ID. Pinless benchmark evaluation disables pin and recency boosts.
6. **Keep no-match explicit.** Return `direct_answer_found` only if an accessible current card qualifies as `direct_answer`; otherwise return `no_match`, while optionally returning separately classified `related` cards with their mismatch reason. An empty corpus is `no_match`.
7. **Record exposure before supplying outcomes.** Commit a retrieval event with the query, structured scope, accessible candidate set, ranking, mismatch reasons, supplied refs, and exposure before adding card content to worker context. Append the later worker use/rejection rationale as an immutable disposition linked to that event.
8. **Keep story lookup separate from inventory.** Legacy and intermediate artifacts remain searchable through 019's inventory and are never promoted to answer cards solely from filename or keyword similarity. The 99-file acceptance fixture verifies both paths.
9. **Keep fixture labels private.** Tests and live demonstrations use synthetic/local data. Relevance labels and reference answers stay in the evaluator path and are absent from worker context. The local shell guarantee is reported as workflow-level where applicable.

## Project Structure

### Planning artifacts

```text
specs/020-find-analytical-answers/
├── plan.md
├── research.md
├── data-model.md
├── contracts/retrieval-extension.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Feature implementation outputs

```text
src/analytics/
├── answers.ts
├── retrieval.ts
└── acceptance/020.test.ts
```

**Structure Decision**: Follow the frozen graph-owned TypeScript facades. Finalization and durable fallback live in `answers.ts`; search, ranking, project-home reads, and `analyticsExtensionV1` live in `retrieval.ts`; only feature-local synthetic acceptance cases live in `acceptance/020.test.ts`.

## Post-Design Constitution Check

The design records answer content separately from evidence and Claim state, filters access before ranking, preserves curation and exposure as history, calls 023 matching once through its upstream handoff, and makes 009/019 the only durability and index owners. The later worker path and viewer references provide a real producer, consumer, user inspection route, and injected failures. No unresolved design decision remains.

## Complexity Tracking

No constitution violations or extra projects/services are introduced.
