# Tasks: 018 â€” Whole-product evaluation and release assessment

## Execution amendment — 2026-10-09

The unit order below and execution.json supersede earlier checklist ordering. Product spec.md, shared interfaces, thresholds and original final acceptance remain unchanged. Original task text and exact split mappings are retained in docs/build/dependency-replan.json. Completed tasks retain their recorded state. Every implementation unit includes its own passing positive and failure controls; fixtures for later production paths run with their producers.

## scope-t004

- [ ] T004 [US1] Implement pre-batch registry, comparison and audit-sample validation in `src/analytics/evaluation/release.ts`; require the locked formula, numerator, denominator, direction, minimum, threshold, profile, evaluator/fixture refs, approval by the configured project owner under feature 010 FR-008 and approval time before batch start, reject mismatched fixtures/tasks/harness/budgets/sources/adjudication, and record the frozen risk-stratified disagreement sampling frame, selection, adjudicator, severity counts and uncertainty.

## scope-t001

- [ ] T001 [US1] Implement the version-pinned batch runner and evaluator-only reference boundary in `src/analytics/evaluation/evaluator.ts`; invoke the production worker/controller path with only each synthetic question, granted source and declared allowance, record fixture/source/grant/model/reasoning/harness/prompt/environment/budget settings and actual packet digest, and read answer keys or planted-fault definitions only after the worker response.

## scope-t002

- [ ] T002 [US1] Score every complete question and evidence chain in `src/analytics/evaluation/evaluator.ts`; compute exact numerators, denominators, units and evidence refs for answer quality, unsupported promotion, fault recall, false blocking, trace completeness, budget completion, elapsed work and cost, preserving missing telemetry as `unavailable`, all valid negative/null/uncertain states, and failed or incomplete cases.

### Phase 4: User Story 2 - Compare source memory value (Priority: P2)

**Goal**: Establish whether compatible memory and skills reduce rediscovery
without harming answer quality, access boundaries or required checks.

**Independent Test**: Run matched cold-source and warm-source inquiries for
the same frozen questions, source versions and allowances. Report discovery
effort, answer quality, required-check omissions, retrieval/candidate/skill
reuse and incompatible-scope errors together with explicit denominators.
The locked retrieval and capture cohorts retain their specified 150-card/20
query, five no-match, and 48-signal/32-positive sample sizes and thresholds.

## retrieval-scoring

- [ ] T003 Score the frozen 150-card/20-query/five-no-match cohort from actual whole-product retrieval evidence; record Hit@3 numerators, denominators, automatic recall and zero false direct answers with current access checks and exact fixture refs. Preserve failed/incomplete cases.
  - Files: `src/analytics/evaluation/evaluator.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## skill-scoring

- [ ] T009 Score 48-signal precision/32-positive recall, independent validation, later activation/use, incompatible reuse and matched cold/warm discovery effort/quality/check retention from feature-owned committed evidence. Retain exact fixed denominators and unavailable telemetry.
  - Files: `src/analytics/evaluation/evaluator.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## artifact-scoring

- [ ] T010 Score the 99-file import/false-story corpus, retained file count/bytes, unwanted CSV/XLSX exports, notebook replay and unmanaged writes from feature-owned evidence. Separate observation from enforced containment and preserve missing telemetry.
  - Files: `src/analytics/evaluation/evaluator.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## ledger-access-scoring

- [ ] T011 Score ledger completeness/exact-repeat/false-duplicate/prior-negative retrieval plus grant-respecting source/context/viewer behavior from production evidence. Keep exact cohort and evidence refs; unavailable required observations cannot pass release.
  - Files: `src/analytics/evaluation/evaluator.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## scope-t005

- [ ] T005 [US1] Implement fail-closed `ReleaseAssessmentV1` construction in `src/analytics/evaluation/release.ts`; report each metric's exact calculation and evidence, fail on missed thresholds, small/zero denominators, missing required observations, reference/context integrity failures or post-batch approval, label undersized disagreement audits descriptive only, and expose separate fixture-profile and general-reliability eligibility without changing any analytical Claim or policy threshold.

## scope-t006

- [ ] T006 [US1] Add acceptance scenarios A01-A05 and A09-A10 in `src/analytics/acceptance/018.test.ts` for complete question/evidence scoring, unavailable cost, exact missed-gate reporting, answer-key/fault-description exclusion from the actual worker packet, complete worker-setting capture, immutable fixture/rubric/grant binding, and post-start reference mutation invalidation; do not stub or synthesize the worker answer.

## scope-t007

- [ ] T007 [US2] Add acceptance scenarios A06-A08 and A11-A17 in `src/analytics/acceptance/018.test.ts` for development/held-out separation, matched and mismatched comparisons, too-small disagreement samples, always-block false-block failure, answer retrieval/catalog/false-story metrics, skill capture and reuse denominators, per-profile export/replay/unmanaged-write metrics, ledger denominator coverage, metric approval completeness, and broad-claim rejection.

## scope-t008

- [ ] T008 Run `pnpm test` and `pnpm smoke:viewer` for the complete 018 acceptance path, including the real held-out synthetic worker run with `gpt-6-luna`/`xhigh`, then record actual command outcomes, batch/profile and fixture-lock references, settings, numerators/denominators, failed gates and evidence references for every FR, SC and Edge Case in `specs/018-evaluate-analytics-product/verification.md`.

## Dependencies

| Unit | Depends on |
| --- | --- |
| scope-t004 | Completed feature prerequisites |
| scope-t001 | scope-t004 |
| scope-t002 | scope-t001 |
| retrieval-scoring | scope-t002 |
| skill-scoring | retrieval-scoring |
| artifact-scoring | skill-scoring |
| ledger-access-scoring | artifact-scoring |
| scope-t005 | ledger-access-scoring |
| scope-t006 | scope-t005 |
| scope-t007 | scope-t006 |
| scope-t008 | scope-t007 |

## Canonical decomposition: evaluation-cohort-acceptance

- [ ] T012 Complete original T007 catalog/false-story/retrieval, skill capture/reuse, export/replay/unmanaged-write and ledger denominator acceptance, plus approval completeness and broad-claim rejection. Use already implemented scoring providers and fixed fixture denominators.
