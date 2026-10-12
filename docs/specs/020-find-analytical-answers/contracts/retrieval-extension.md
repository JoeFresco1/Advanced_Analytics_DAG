# Contract: 020 Answer Retrieval Extension

This contract consumes `docs/build/interfaces.md` version 1. It does not redefine the shared extension registry, project-memory commit protocol, 019 catalog, 023 match record, canonical states, or 011 HTTP/viewer API.

## Provider identity

- Module: `src/analytics/retrieval.ts`
- Export: `analyticsExtensionV1: AnalyticsExtensionV1`
- `extensionId`: `answers`
- `dependsOn`: `catalog`, `ledger`
- Shared registry/lifecycle dispatcher: 003 `extensions.ts`
- Durable answer/fallback/curation builder: `src/analytics/answers.ts`
- Project-memory commit/recovery: 009
- Candidate index and current access filtering: 019
- Upstream prior-attempt match: 023

## Commands

### `dag analytics answers search --query <text> [--source <id>] [--metric <id>] [--period <range>] [--claim-state <state>] [--pinned <true|false>]`

Resolve the authenticated principal from the shared command context. Call 019's accessible search/filter path before returning card IDs, titles, ranking features, or worker context. Return the query outcome, per-card classification, exact relevance or mismatch reason, current validity, permissible scope, stable card/evidence refs, and work-ledger link. Related cards may appear in a separate section when no direct answer exists.

### `dag analytics answers home`

Return the access-filtered project-home projection without requiring a query. Include recent, pinned, and unresolved answer cards with stable links, including negative and unresolved findings. The command does not rank through inaccessible candidates or alter the 011 viewer API.

## Per-card classification and ranking

1. Ask the current 019 index for accessible candidates; restricted candidates and metadata are absent before ranking.
2. Apply requested structured filters against source, metric, period, Claim state, and pin state.
3. Compare known question intent, metric, population, period, source meaning, current evidence, and current validity. A known compatible direct candidate may be `direct_answer`; a materially related candidate with a known mismatch is `related` with that mismatch; a card without a substantive question connection is `not_relevant`.
4. Missing required compatibility is `related` at most, never `direct_answer`. Stale/superseded or unavailable evidence cannot be presented as current support.
5. Order direct candidates ahead of related cards. Within an equal relevance/validity/scope tier, use deterministic structured and lexical relevance, then optional pin or recency tie-breaks, then stable card ID. Pin and recency are disabled for the frozen benchmark.
6. Return `direct_answer_found` only if at least one accessible card qualifies as a direct answer. Otherwise return `no_match`; related cards may be included separately with their reason. Empty corpus returns `no_match`.

The 150-card/20-query relevance labels are evaluator-only. Acceptance computes Hit@3 as qualifying queries divided by 20, requires at least 18, and runs with pins disabled. Five frozen no-match queries, including an empty corpus, require zero false direct answers. Ranking code and labels are not retuned or rewritten after observing results.

## Lifecycle and exactly-once handoff

### `orient(context)`

003 dispatches 023's ledger `orient` first because the provider dependency order places `ledger` before `answers`. Consume the already-produced `AnalyticalMatchV1` assessment as a read-only input; do not call the 023 matcher, identity predicate, or ledger orientation operation again. Then query 019, rank accessible answer cards, commit one stable `RetrievalEventV1` through 009, and return bounded answer refs/content, reasons, scope/current-validity, evidence refs, and the retrieval/exposure refs. The worker receives only access-permitted material.

The acceptance fixture instruments the service boundary and asserts exactly one 023 prior-attempt match call and exactly one retrieval event for an inquiry orientation. 020's ranking may not reinterpret a known mismatch into a direct answer.

### `beforeFinalize(context, terminalIntentRef)`

Call the feature-local finalizer in `answers.ts`. It builds one `AnswerCardV1` from committed facts, commits it and any requested curation event through 009, and returns durable project refs to 003. If the worker summary is absent, incomplete, or interrupted, create the specified deterministic fallback without inventing an interpretation. Read an explicit “remember this analysis” instruction from the already-committed terminal-intent/user-request references and append a pin event for the resulting card; do not add a field to the frozen 003 terminal-intent contract. It does not create a skill or change support/scope. Append worker use/rejection rationale as a new disposition linked to each surfaced retrieval event. A replay uses stable IDs and does not duplicate cards, pins, exposures, or dispositions. A commit failure prevents completion acknowledgement.

## Durable record boundaries

- 009 is the sole commit/CAS/recovery owner for answer, curation, retrieval, and disposition records.
- 019 indexes only committed records and curation history; its SQLite/FTS5 index is rebuildable and never becomes authoritative.
- Answer IDs and evidence refs remain stable across index rebuilds. Corrections create linked versions/current-validity events rather than rewriting historical cards.
- Current access is checked for search, home, answer, timeline, and artifact paths. A safe historical pointer can remain after redaction/revocation, without protected values or paths.
- Retrieval/exposure is committed before a surfaced outcome enters worker context; later worker use is a separately attributable record.

## Acceptance obligations

`src/analytics/acceptance/020.test.ts` covers all 020 FR/SC/edge cases, including:

- 99 readable mixed CSV/workbook files, duplicates, and unknown provenance; question/period search finds the answer without a filename while inventory outputs do not become answer stories.
- Frozen 150 cards and 20 annotated queries achieve Hit@3 ≥ 0.90 with no pin boosts; returned cards include relevance and mismatch reasons.
- Five no-match queries, including empty corpus, produce zero false direct answers; restricted cards/titles do not enter results.
- Pinless ranking, tie-only pin behavior, filters, access-before-ranking, no-match, deterministic fallback, pin/annotation rebuild, stale/redacted availability, project home, and ledger timeline links.
- Later related inquiry with no user hint receives compatible prior work and records use/exposure; wrong cohort or stale evidence is not reused as current support.
- One 023 match handoff and one retrieval event; a worker-without-summary/incomplete path remains durably acknowledged only after answer commit.

The final acceptance unit also records the required live `gpt-6-luna`/`xhigh` later-worker demonstration using only the synthetic/local fixture and records actual harness/context/evidence refs in `verification.md`.
