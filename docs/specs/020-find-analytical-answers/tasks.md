# Tasks: 020 â€” Slice B2 answer cards, ranking and automatic recall

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## durable-answer-cards

- [ ] T001 [US1] Add synthetic acceptance cases in `src/analytics/acceptance/020.test.ts` for durable scoped cards, negative/inconclusive outcomes, competing Claims and selected-finding reasons, evidence/artifact refs, and opening an answer's ledger link to see every admitted attempt's method, outcome state, and evidence refs, plus worker-without-summary/interrupted-finalization fallback and no invented interpretation.
- [ ] T002 [US1] Implement `AnswerCardV1` construction, deterministic committed-fact fallback, a ledger-timeline link derived from the existing inquiry reference/path plus evidence/artifact refs, selected-story reasons, and idempotent finalization persistence through 009 in `src/analytics/answers.ts`; preserve canonical inquiry/evidence/current-validity states and leave unsupported or missing interpretation explicit.

## ranked-search

- [ ] T003 [US1] Add deterministic retrieval contract cases in `src/analytics/acceptance/020.test.ts` for question/source/metric/period/Claim-state/pin filters, access-before-ranking, direct/related/not-relevant classification, unknown compatibility, reasoned no-match, tie-only pins, restricted metadata exclusion, and the frozen 99-file, 150-card/20-query, and five-query no-match criteria.
- [ ] T004 [US1] Implement structured filters, access-first candidate retrieval through 019, deterministic pinless relevance ranking, canonical per-card classification, explicit `direct_answer_found`/`no_match`, and the feature-owned `analyticsExtensionV1` search command in `src/analytics/retrieval.ts`; pins/recency may break ties only, and unknown scope may never be direct.

**Checkpoint**: User Story 1 returns question-searchable scoped answers from the 99-file corpus, preserves negative/inconclusive findings, passes the frozen ranking/no-match limits, and keeps inaccessible candidates out of output and context.

## curation-home

- [ ] T005 [US2] Add synthetic curation and home cases in `src/analytics/acceptance/020.test.ts` for pin/annotation/supersession rebuild, an explicit “remember this analysis” pin during finalization, unchanged Claim state/scope, stale and redacted evidence, recent/pinned/unresolved home cards including negative and unresolved answers, and stable answer/timeline links.

### Implementation for User Story 2
- [ ] T006 [US2] Implement append-only pin/annotation/supersession events, pin-on-finalization handling from existing user-request/terminal-intent refs (without adding a frozen 003 schema field), and the access-filtered no-query `answers home` projection in `src/analytics/answers.ts` and `src/analytics/retrieval.ts`, persisted through 009 and rebuildable through 019 without changing Claim support, scope, or validity.

**Checkpoint**: User curation and home views survive an index rebuild, current access is rechecked, and corrected/redacted evidence remains historically inspectable with canonical status.

## automatic-recall

- [ ] T007 [US3] Add integration cases in `src/analytics/acceptance/020.test.ts` for the 023-to-020 orientation handoff, exactly one 023 prior-attempt match and one retrieval event, prior-outcome exposure, attributed worker use/rejection, no-hint later recall, wrong-cohort and stale-card rejection, and bounded context under many related cards.

### Implementation for User Story 3
- [ ] T008 [US3] Implement dependency-ordered `orient` and `beforeFinalize` hooks in `src/analytics/retrieval.ts` that consume the already-produced 023 `AnalyticalMatchV1` without calling its matcher again, use 019's access-filtered candidate IDs, durably record retrieval/exposure and linked later disposition through 009, and call `answers.ts` to commit the card/fallback before 003 acknowledges completion.

**Checkpoint**: A later live worker in the same synthetic project finds relevant prior work with no user hint, while recorded access, current validity, mismatch, exposure, and use/rejection remain attributable.

## acceptance-verification

- [ ] T009 Run `pnpm build` and `pnpm test`, execute the 99-file, 150/20, five no-match, curation/rebuild, project-home, every-admitted-attempt timeline, and crash/fallback acceptance paths, and perform the two-inquiry live recall demonstration with `gpt-6-luna`/`xhigh` using only synthetic/local data; write actual command/results, Hit@3 denominator, per-query outcomes, one-match/one-retrieval evidence, harness/context/source digests, evidence refs, and FR/SC/edge-case evidence plus limits to `specs/020-find-analytical-answers/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| durable-answer-cards | Completed feature prerequisites |
| ranked-search | durable-answer-cards |
| curation-home | ranked-search |
| automatic-recall | curation-home |
| acceptance-verification | automatic-recall |
