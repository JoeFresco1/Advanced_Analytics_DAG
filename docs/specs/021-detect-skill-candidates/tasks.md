# Tasks: 021 â€” Automatic skill-candidate capture from feedback and reuse

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## provider-foundation

- [ ] T001 Implement `analyticsExtensionV1` metadata and idempotent bounded checkpoint hooks in `src/analytics/skill-capture.ts`. Use `afterEvidenceCommit` for committed 003 inquiry/evidence records; use dependency-ordered `beforeFinalize` after the 020 `answers` finalizer to consume the current committed answer, a bounded set of completed question/answer refs, and the ordinary current terminal-intent/user-request ref. This ref is the producer when a follow-on question gives feedback on an already completed answer; join it through an explicit answer ref or the exact card ref in 020's retrieval/exposure event, and suppress a method candidate if the target is ambiguous. Read 014 candidate source packages through the 015 `source-reuse` facade; persist capture records through 009. Do not add a command, callback registry, or shared dispatch edit.

**Independent verification**: `pnpm build` compiles the provider against the frozen extension types; no CLI/controller or persistence files change.

## method-endorsement

- [ ] T002 [US1] Add synthetic acceptance cases for feedback carried by an ordinary follow-on delegated question, its 003 finalization hook path, method endorsement, identical-signal replay, and answer-only praise routed through the 020 answer facade in `src/analytics/acceptance/021.test.ts`.
- [ ] T003 [US1] Classify feedback from terminal-intent/user-request refs against the exact completed answer record; persist a typed candidate or route answer-only praise to the answer-card annotation path, with trigger/evidence refs, in `src/analytics/skill-capture.ts`.

**Checkpoint**: User Story 1 is independently verifiable after T001–T003 with `pnpm exec tsx --test --test-name-pattern=capture-US1 src/analytics/acceptance/021.test.ts`.

## repeat-trigger

- [ ] T005 Recognize repeated validated work across bounded completed inquiry/answer records in src/analytics/skill-capture.ts. Persist candidate-only decisions with exact scope, evidence and validation gaps through 009; use stable signal IDs to prevent duplicate proposals. Test valid repetition, insufficient/invalid repetition and replay.

## cost-trigger

- [ ] T009 Recognize costly source discovery only against predeclared thresholds and committed source-package evidence exposed through 015 in src/analytics/skill-capture.ts. Preserve intended grant/source scope and exact measured cost/count evidence; absent thresholds or telemetry cannot qualify. Test valid cost, missing threshold, missing evidence and replay without duplicate proposals.

## correction-trigger

- [ ] T010 Recognize durable corrections only with independent validation and later-reuse evidence in src/analytics/skill-capture.ts. Keep exact owner/conflict and prior-outcome exposure refs; suppress unresolved authority conflict and protected holdout signals; enforce checkpoint budgets and dedup/revision links. Test a validated correction, unvalidated correction, conflict, restricted exposure and exhaustion.

## scope-t004-t006

- [ ] T004 [US2] Add synthetic acceptance cases for bounded completed-question repetition, source-package threshold evidence through 015, durable correction, dedup/revision, owner conflict, holdout exposure, scope restriction, and budget exhaustion in `src/analytics/acceptance/021.test.ts`.
- [ ] T006 [US2] Persist typed proposal/suppression decisions with exact scope, authority conflict refs, prior-outcome exposure, dedup/revision links, validation gaps, and controller-supplied checkpoint limits in `src/analytics/skill-capture.ts`; keep all outputs candidate-only.

**Checkpoint**: User Story 2 is independently verifiable after T001–T006 with `pnpm exec tsx --test --test-name-pattern=capture-US2 src/analytics/acceptance/021.test.ts`.

## frozen-evaluation

- [ ] T007 Consume the frozen `src/analytics/evaluation/fixtures/capture-signals.json` cohort through `src/analytics/evaluation/registry.ts` in `src/analytics/acceptance/021.test.ts`: pin/select the unchanged 48-signal set, run capture before evaluation, resolve evaluator-only references after each completed response, and call `adjudicateResponseV1`. Apply the registered candidate-precision/recall formulas without exposing labels to capture. Preserve eight positives per trigger, eight negative answer-only praise signals, eight negative unvalidated-significance signals, and all existing gates. Report proposal IDs, true-positive count, total proposals, all 32 positive signals, precision, recall, and duplicate-proposal count.
- [ ] T008 Run `pnpm build` and the complete 021 acceptance file with `pnpm exec tsx --test src/analytics/acceptance/021.test.ts`; update `specs/021-detect-skill-candidates/verification.md` with actual command/evaluator evidence and pass/fail status for every FR, SC, and ordered edge case.

**Final gate**: The frozen evaluation fails closed for missing labels/denominators, fewer than 10 proposals, precision <0.80, recall <0.80, or any duplicate proposal. Every 021 acceptance case must run; skipped/TODO cases do not pass.

## Dependencies

| Unit | Depends on |
| --- | --- |
| provider-foundation | Completed feature prerequisites |
| method-endorsement | provider-foundation |
| repeat-trigger | method-endorsement |
| cost-trigger | repeat-trigger |
| correction-trigger | cost-trigger |
| scope-t004-t006 | correction-trigger |
| frozen-evaluation | scope-t004-t006 |
