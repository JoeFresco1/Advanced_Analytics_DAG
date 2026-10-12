# Implementation Plan: Evaluate Complete Analytics Work

**Branch**: `[018-evaluate-analytics-product]` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `/specs/018-evaluate-analytics-product/spec.md`

## Summary

Feature 018 consumes the frozen fixture registry and reference-evaluator API owned by `18-bootstrap`, invokes the real analytics worker path on synthetic held-out questions, and scores complete question-to-answer evidence. It records comparable worker and allowance settings, scores memory, ledger, skills, outputs, access, time and cost, and applies only the versioned, project-owner-approved metrics that were frozen before a batch. A release assessment exposes exact numerator, denominator, threshold, metric evidence, and failures. A passing batch establishes eligibility for the named fixture profile only; it cannot establish general reliability from the 20-case minimum.

Reference keys and fault descriptions remain evaluator-only. The evaluator reads them only after the worker responds, binds the batch to fixture, generator, rubric and evaluator digests, and invalidates a batch if a reference changes. The current local shell profile is workflow-withheld and does not enforce filesystem isolation; this limitation is part of every report. Runtime answers and evidence come from the worker and committed product records, never fixture-only expected answers.

## Technical Context

**Language/Version**: TypeScript, Node.js 20 or later.

**Primary Dependencies**: Existing TypeScript/Node test toolchain; `18-bootstrap` frozen registry, fixture lock and base evaluator API; `003` worker/controller and CLI; existing catalog, ledger, skill, access, budget and evidence facades. No new package dependency.

**Storage**: Versioned evaluation records and immutable evidence references from the existing run/project sidecars and bootstrap fixture lock. The evaluator may emit a structured report artifact; it does not add a parallel authoritative store or edit the registry/lock.

**Testing**: `pnpm typecheck`, `pnpm test` (including `src/analytics/acceptance/018.test.ts`), and `pnpm smoke:viewer`. The acceptance path must execute the real worker using model `gpt-6-luna` and reasoning `xhigh` on held-out synthetic questions.

**Target Platform**: Local analytics CLI and controller using the neutral execution package and synthetic/local fixtures.

**Project Type**: TypeScript analytics controller/evaluation service.

**Performance Goals**: Complete the frozen batch within each case's declared enforceable time and invocation allowance; reserve capacity for required checks and final reporting. Report actual elapsed work. Do not claim an exact monetary/token cap without enforceable telemetry.

**Constraints**: Preserve accepted reference answers, fixture IDs, rubrics, grant profiles, metric thresholds, minima and approval records. Answer keys stay outside worker context; local filesystem visibility is reported honestly. Cost is `unavailable` when unobserved. Missing required observations, invalid references, post-batch approvals, and undersized denominators fail closed. Fault recall must be assessed with false blocking. Keep analytical outcomes distinct from process status and release eligibility.

**Scale/Scope**: First tabular frozen-fixture profile, including at least 20 complete questions plus the registry's dedicated answer-retrieval, artifact/import, skill-candidate, ledger, output and access cohorts. General reliability is a separate assessment requiring a predeclared population, sample-size rationale, uncertainty interval and separate held-out batch.

## Constitution Check

- **Question-level honesty**: Score the actual scoped answer, uncertainty, claim evidence and limitations. A successful process is not a correct answer.
- **Evidence and history**: Bind each observation to the run, inquiry, fixture and evidence references; preserve failed, null, negative, conflicting, blocked and incomplete cases.
- **No favorable retrying**: Evaluation records a bad or inconclusive worker response as evidence. It does not rerun until favorable.
- **Memory scope**: Score answer cards, ledger attempts, outputs and skills with their versions, current validity, grants and exposure. Do not turn answers into reusable guidance by implication.
- **Accurate guarantees**: Report reference isolation as workflow-withheld where local worker filesystem access is not enforced. Report unknown costs as unavailable.
- **End-to-end path**: Run the real worker and verify its committed answer/evidence path before the independent evaluator scores it.
- **Governance**: Thresholds are immutable inputs from the approved registry. Only the configured project owner may approve a version before the batch; implementation results do not change policy.

**Gate result**: Pass. The frozen architecture gives ownership to 18-bootstrap for registry, fixture package and lock, and to 018 for scoring and release assessment. The plan adds no new scheduler, run-file fields, access grant, reference answer, or policy authority.

## Project Structure

### Documentation (this feature)

```text
specs/018-evaluate-analytics-product/
├── plan.md
├── research.md
├── data-model.md
├── contracts/evaluation-v1.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Source Code (repository root)

```text
src/analytics/evaluation/
├── evaluator.ts       # Worker-run orchestration, isolation boundary and scoring
└── release.ts         # Registry validation, comparison/audit rules and eligibility

src/analytics/acceptance/
└── 018.test.ts        # Frozen synthetic acceptance and live worker path
```

**Structure Decision**: Keep evaluation under `src/analytics/evaluation/`. `18-bootstrap` remains sole owner of `registry.ts`, fixtures and `docs/build/evaluation-fixture-lock.json`. Feature 018 owns only `evaluator.ts`, `release.ts`, its acceptance test and feature planning/evidence documents, as assigned by `docs/build/graph.json`. Reuse worker/controller, evidence, catalog, ledger, access, skill and budget providers through frozen facades; do not add a new CLI/controller dispatch seam or change shared files.

## Design Gates

1. **Frozen inputs**: Resolve every scored fixture, generator, rubric, grant profile, reference evaluator and metric entry to the registry's immutable IDs and digests before work starts.
2. **Pre-batch authority**: Verify formula, numerator, denominator, direction, minimum cohort, threshold, profile, owner and approval time. Require the approval timestamp to precede batch start and the actor to be the configured project owner under feature 010 FR-008. Never use a later threshold to judge an earlier batch.
3. **Worker isolation**: Compile only the question, granted source, permitted context and allowance into the real worker request. Verify the actual packet digest/reference-exclusion audit before consulting expected answers. Any key/fault-description exposure invalidates the batch.
4. **Scoring**: After the worker responds, resolve the independent rubric, score complete answer/evidence states and compute counts with explicit denominators. Preserve missing observations as missing, not zero.
5. **Comparison and audit**: Reject incomparable runs with different task, fixture, budget, harness, source or adjudication constraints. Draw disagreement audits from the predeclared risk-stratified rule and label samples below their precision target descriptive only.
6. **Release scope**: A complete passing initial registry can mark only the named fixture profile eligible. A broad reliability request additionally needs its predeclared target population, rationale, interval and separate held-out batch.

## Constitution Check (post-design)

**Gate result**: Pass. The design preserves accepted contracts and evidence history, reports unfavorable outcomes, uses owner-approved pre-batch metrics, separates process/claim/release facts, and does not overstate local holdout or broad reliability guarantees. No constitution exception is requested.

## Complexity Tracking

No constitution violations or additional runtime architecture are proposed.
