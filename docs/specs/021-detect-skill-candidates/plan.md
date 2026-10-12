# Implementation Plan: Detect Reusable Skill Candidates

**Branch**: `021-detect-skill-candidates` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Accepted feature specification at `specs/021-detect-skill-candidates/spec.md`.

## Summary

Implement bounded result and feedback checkpoints in the `skill-capture` analytics extension. Feature 003 dispatches `afterEvidenceCommit` for committed evidence and `beforeFinalize` in dependency order. Feedback about an already completed inquiry arrives in the analyst's next ordinary delegated question/request and is present in its terminal-intent ref. When that follow-on inquiry finalizes, the 020 `answers` hook has committed its answer card; 021 consumes that card, the request/feedback ref, and bounded completed inquiry/answer records. It joins feedback to a prior card through an explicit answer ref or the exact card ref in 020's retrieval/exposure event; an ambiguous target cannot create a method candidate. The registered 015 `source-reuse` facade exposes committed candidate source packages produced by 014. This is the production signal and dispatch path: no separate feedback command or callback is introduced. The provider recognizes four typed positive signals, keeps answer memory separate from reusable guidance, and commits candidate or suppression records through feature 009 project memory. Candidates remain `candidate`; feature 021 has no activation path. The final acceptance slice consumes the unchanged 48-signal fixture through the 18-bootstrap evaluator API and reports the specified precision, recall, and duplicate denominators.

## Technical Context

**Language/Version**: TypeScript, Node.js 20+; ESM project.

**Primary Dependencies**: Graph prerequisites 003, 014, 015, and 020. The provider consumes the frozen 003 `AnalyticsExtensionV1` registry and lifecycle dispatch, 020 `answers`, and 015 `source-reuse` (which exposes 014's committed candidate source packages); persistence uses 009 `commitProjectMemory` / `recoverProjectMemory`. Evaluation uses the 18-bootstrap `src/analytics/evaluation/registry.ts` API and locked capture fixture; whole-product release aggregation remains owned by 018. No new runtime dependency or shared registry edit.

**Storage**: Versioned project-memory records committed with feature 009's append-only compare-and-swap primitive. SQLite/catalog indexes remain rebuildable projections. Feedback annotations and study-specific results remain in 020 answer records; candidate source packages remain 014 records surfaced through the 015 facade. No state is added to `dag.run.json` and no second persistence mechanism is introduced.

**Testing**: TypeScript build and Node test runner. The feature-owned acceptance file is `src/analytics/acceptance/021.test.ts`; it uses only synthetic/local records and the 18-bootstrap evaluator API. Focused unit checks use `pnpm exec tsx --test` against that acceptance file; final acceptance runs the complete 021 file. This planning stage does not run product tests.

**Target Platform**: Local analytics product on Node.js 20+ with the existing `pnpm` workspace.

**Project Type**: Internal TypeScript analytics extension and acceptance fixture.

**Performance Goals**: Every checkpoint terminates inside its policy-supplied fixed time and work allowance; repeated capture does not consume the capacity reserved for final reporting.

**Constraints**: Preserve the 48-signal labels, policy version, denominators, precision threshold (≥0.80 on ≥10 proposals), recall threshold (≥0.80 across all 32 positives), and zero-duplicate gate. Do not tune labels or thresholds. Candidate generation is detection only; 022 owns activation. Do not edit the frozen registry, persistence primitive, specs, checklist, graph, shared architecture, package manifests, `dag.run.json`, or other feature outputs. Do not retry a valid negative signal for a favorable result. Scope and exposure remain explicit, and unknown facts remain unknown.

**Scale/Scope**: One provider module, one feature acceptance test, and project-scoped immutable capture records. The frozen evaluation batch has 48 locked signals: eight positive examples for each of the four trigger types, eight negative answer-only praise signals, and eight negative unvalidated-significance signals.

## Constitution Check

- **Question and evidence remain honest**: A signal is an attributed reuse cue with evidence refs, not proof of analytical support. Negative, null, inconclusive, and failed outcomes are retained as answer memory and never become favorable-skill evidence by process status alone.
- **History and exposure remain immutable**: Candidate revisions link prior candidates. Outcome-bearing references retain exposure provenance and remain study-scoped; they are not copied into general method instructions.
- **Memory is scoped and tested**: Candidate scope, audience, trigger, validation gaps, owner conflicts, and evidence are explicit. No access grant is widened. Duplicate suppression and its reason are durable.
- **Autonomy is bounded**: Capture reads the checkpoint's fixed time/work allowance and leaves finalization capacity available. Exhaustion emits a bounded suppression/defer outcome and does not retry.
- **End-to-end ownership is respected**: The provider consumes registered answer/source-reuse data, writes via 009, and has a feature-owned acceptance path. The shared 003 registry and 009 persistence are consumed rather than reimplemented.

**Gate**: Pass. No constitution exception or unresolved design question is required by the accepted contract.

## Design Decisions

1. Use only the existing typed extension seam. Register `extensionId: "skill-capture"`, with dependencies `answers` and `source-reuse`; use `afterEvidenceCommit` for committed-result checkpoints and `beforeFinalize` for feedback checkpoints. An analyst's ordinary next delegated question/request is the producer for feedback about a prior completed answer; 003's existing terminal-intent/user-request ref carries it. The `beforeFinalize` hook runs after the 020 answer-card finalizer because of dependency order. It reads the new request and committed answer refs plus a bounded set of completed question/answer records from the `answers` facade. It joins feedback to a prior answer only through an explicit ref or the exact card ref in 020's existing retrieval/exposure event, never by approximate matching inside 021. This gives post-completion feedback an existing producer and dispatch path without adding a command, callback registry, or controller edit.
2. Treat a positive capture signal as one of `method_endorsement`, `repeated_validated_work`, `costly_discovery`, or `validated_correction`. Answer-only praise is an answer-card annotation/suppression case. Statistical significance, model confidence, file count, and praise without method/rule endorsement cannot independently create general guidance.
3. Build signals from committed 003 inquiry/evidence records and 020 answer records; read committed 014 candidate source packages through the 015 `source-reuse` facade. Require exact evidence refs and an explicit signal threshold or validation evidence. Candidate fields include trigger, skill type, intended audience, exact source/role/period scope, expected reuse, validation gaps, and conflict/authority refs when present.
4. Deduplicate on the typed procedure identity plus scope. Identical input yields one candidate and a durable suppression/link reason; material procedure changes create a linked candidate revision. Do not key identity on free-form praise text alone.
5. Narrow candidate scope to the supporting evidence. A source role or time period tested once stays that narrow. A conflict with an owner-curated definition names its definition and owner and remains a candidate for 022; no authority choice is made here.
6. Keep bound prior outcomes as separately identified answer/evidence references with exposure provenance. An outcome cannot enter generic reusable instructions; if it cannot be separated safely, suppress reusable-body capture and retain only answer memory.
7. Use only the budget and frozen capture-policy version supplied by the controller for signal count/time ceilings. Bound the completed question/answer records considered at each checkpoint by that allowance. No new candidate-specific numeric policy threshold is invented by this feature plan. An exhausted allowance produces an explicit defer/suppression record and cannot consume final-report capacity.
8. Persist through 009 project commits with stable event/commit identities. On replay, use the same identities; on revision conflict, preserve both histories and follow the shared CAS contract.

## Project Structure

### Documentation (this feature)

```text
specs/021-detect-skill-candidates/
├── plan.md
├── research.md
├── data-model.md
├── contracts/skill-capture-v1.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md
```

### Source Code

```text
src/analytics/
├── skill-capture.ts                 # Feature 021 extension and capture policy
└── acceptance/
    └── 021.test.ts                  # Signal, checkpoint, scope, and evaluator acceptance
```

**Structure Decision**: Follow the frozen graph ownership exactly. Keep all domain capture behavior in `src/analytics/skill-capture.ts`; do not edit `extensions.ts`, controller/CLI files, persistence, answer-card, source-reuse, activation, fixture-registry, or package files. The final unit owns the feature acceptance test and records its actual results in this feature's `verification.md`.

## Constitution Check After Design

Pass. The design keeps proposal, result memory, evidence, exposure, scope, authority, activation, and persistence separate. Its checkpoint limits come from frozen controller policy; evaluation uses the locked 18-bootstrap capture cohort and gates without changing any labels or thresholds. There are no justified violations to track.
