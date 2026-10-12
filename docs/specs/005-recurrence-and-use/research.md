# Research: Recurrence and Consequential Use Integration

## Decision: Compose the frozen feature providers

**Decision**: Use the existing 015 source-skill compatibility/reuse provider, 016 recurrence and invalidation provider, 017 use-authorization provider, 009 committed-record services, and 011 viewer. Keep this feature's production outputs in `src/analytics/demo-recurrence.ts` and `src/analytics/acceptance/005.test.ts` as assigned by `docs/build/graph.json`.

**Rationale**: `docs/build/interfaces.md` assigns each of these transitions to a single owner and freezes the extension lifecycle, stable record references, and committed-read interfaces. `docs/build/ARCHITECTURE.md` identifies 016 and 017 as the first producers consumed by recurrence/use integration. The 005 dependency closure includes 004, 016, 017; 011 is reachable through 004 → 003 → 002 → 011.

**Alternatives considered**: A 005-owned correction traversal, policy evaluator, or viewer page would duplicate owner behavior or edit graph-owned shared files. Both conflict with the frozen ownership contract, so 005 composes the providers and renders their returned records in the existing viewer.

## Decision: Do not require 020 or 023 provider behavior in this feature

**Decision**: Keep answer-card and ledger record references in the 016 correction-impact fixture as opaque committed references. Verify that the current 011 answer/use trace surfaces those permitted links. Do not construct, write, retrieve, rank, match, or refresh answer-card/ledger records in 005.

**Rationale**: The frozen graph has no dependency path from 005 to 020 or 023. 020 owns card creation, retrieval, ranking, and exposure; 023 owns durable attempt history and match assessment. 016 FR-004 owns traversal of their dependent references. Treating these records as synthetic fixture inputs exercises that traversal and viewer link behavior without requiring a later producer or duplicating its semantics. The end-to-end integration can be run after the required 004/016/017 providers and transitive viewer 011 are available.

**Alternatives considered**: Adding dependencies to the manifest is invalid because manifest `dependsOn` is local to its feature, and changing `docs/build/graph.json` is outside the authorized feature directory and conflicts with the instruction to reuse the existing graph. Copying 020/023 producer logic into 005 is prohibited by the interface ownership table. The bounded consumer contract above is the only design that keeps all 005 requirements testable without changing accepted graph ownership.

## Decision: Seed the historical use path before applying a correction

**Decision**: The scenario first commits a report, exact Claim/evidence references, a consequential-use assessment, and an approved Decision through 017. It then commits one or more source corrections through 016 and calls 017's reassessment path.

**Rationale**: 005 SC-005 requires an earlier decision to remain inspectable while its assessment becomes stale. Establishing those 017-owned records before the correction makes ordering explicit and permits exact assertions over the prior decision and current authorization.

**Alternatives considered**: Creating the decision after invalidation cannot demonstrate that the same correction staled a prior assessment or preserve a pre-correction decision. Rewriting the old assessment or Decision would violate append-only history.

## Decision: Reuse canonical vocabularies and exact evidence bindings

**Decision**: Use only the values and transition rules in `docs/specs/state-vocabularies.md`, with `docs/specs/access-and-judgment-contract.md` for actor, principal, grant, reviewer, and policy attribution. Treat availability, reproduction, current validity, Claim evidence, plan exposure, comparison, and authorization as independent dimensions.

**Rationale**: The shared vocabulary document explicitly assigns claim/conflict and authorization transitions to 017, window comparison and invalidation to 016, skill state to 015, and reproduction comparison to 009. The integration records exact Claim, Result, Check, Review, policy, use, Decision, source-window, skill, and Plan references.

**Alternatives considered**: Adding a milestone-specific `corrected`, `approved`, or `verified` status would create a competing vocabulary and conflate evidence, validity, and use. No new state is needed.

## Decision: Use bounded synthetic fixtures and repository commands

**Decision**: Keep the demo fixture local and deterministic. Verify it with the focused Node test, `pnpm build`, `pnpm test`, and `pnpm smoke:viewer` after implementation.

**Rationale**: The acceptance plan requires synthetic/local fixtures, an end-to-end viewer path, and seeded failures. The project already supplies Node's built-in test runner, TypeScript build, and viewer smoke command. The feature introduces no new computational method or dependency.

**Alternatives considered**: External datasets or paid services would violate the bounded fixture requirement and are unnecessary for the recurrence, invalidation, and use transitions. A custom test framework or package dependency would add toolchain changes without improving observability.

## Clarifications

None remain. The exact record owners, execution path, state values, feature prerequisites, output paths, and test commands are specified by the accepted feature spec and frozen build documents.
