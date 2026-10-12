# Implementation Plan: Recurring Data Windows and Invalidation

**Branch**: `016-reconcile-recurring-windows` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

## Summary

Implement immutable recurring-window versions, canonical period-comparison assessments with evidence-backed reasons, declared repeated-inspection policies, late-data correction links, and complete downstream invalidation. `recurrence.ts` exports `analyticsExtensionV1` (`extensionId: recurrence`, dependency `source-reuse`) and participates in the existing 003 registry lifecycle. `invalidation.ts` traverses the nine FR-004 dependent record types and marks each changed-bound dependent `reassessment_required` before any semantic judgment. Run/project records and correction acknowledgements use the append-only publication primitives owned by 009. The acceptance path verifies the provider lifecycle, the answer/trace references, provisional completeness and lateness, and multi-hop corrections using synthetic local records.

## Technical Context

**Language/Version**: TypeScript, Node.js 20+; strict project TypeScript configuration.

**Primary Dependencies**: Existing analytics facades and frozen V1 contracts from 003; 015 source-skill compatibility; 009 `commitEvidence`, `commitProjectMemory`, and recovery/CAS records. No new package or shared-file dependency.

**Storage**: Run-scoped immutable source/window/execution/correction records and project-scoped linked reassessment records in 009 sidecars. The registry/index is not a second source of truth.

**Testing**: Feature-owned Node test fixture `src/analytics/acceptance/016.test.ts`, run directly through the repository's installed `tsx` loader; final verification also runs `pnpm run build` and `pnpm test`.

**Target Platform**: Local analytics CLI/controller and answer/trace viewer routes; synthetic/local fixtures only.

**Project Type**: TypeScript analytics controller library and CLI integration.

**Performance Goals**: Traverse each reachable dependent record and edge once per correction, O(V+E), and preserve all earlier impact references on each successive correction.

**Constraints**: Preserve accepted spec, quality checklist, policies, thresholds, and reference answers. Do not edit `dag.run.json`, invoke a runner, alter the feature graph/shared contracts, change branches, or modify another feature. Do not add software repair semantics. Never use materiality to skip staleness; never rewrite historical records; never classify a valid negative or inconclusive look as an infrastructure retry. Keep event time and processing time separate. Record all comparison/exposure reasons and exact record references. Only committed 009 records are visible.

**Scale/Scope**: Bounded batch windows and the nine canonical dependent types: Results, Claims, Check Results, reports, answer cards, ledger entries, source-skill versions, Use Assessments, and Decision references.

## Constitution Check

### Before design

- **Honest, inspectable answer**: Pass. A window comparison records its scope, canonical state, and reason. A provisional report exposes completeness and lateness limits.
- **Execution distinct from evidence**: Pass. New executions and source versions are separately referenced; correction or a failed check never becomes a retry for a favorable outcome.
- **Immutable history and exposure**: Pass. Recurrences and corrections append new versions; inspection looks and changed-bound invalidation preserve earlier refs and exposure.
- **Scoped memory and authority**: Pass. Recurrence depends on the validated source-reuse provider and records exact skill refs; it does not activate skills or grant access.
- **Bounded accurate guarantees**: Pass. This slice covers finite windows and graph traversal, uses only local synthetic fixtures, and does not claim enforced isolation.
- **End-to-end ownership**: Pass. The module exports through 003's fixed extension registry, consumes 009 persistence, and is observed through answer/trace references and injected correction fixtures.
- **Shared ownership**: Pass. Edits are limited to the feature's graph-owned planning artifacts and three code outputs. No shared architecture, registry/controller, viewer, package, or graph file changes are planned.

No constitution exception or unresolved contract question is required.

### Post-design gate

Pass. Every comparison and validity value comes from the canonical vocabulary. The nine traversal targets match FR-004 exactly. Hook calls use 003's frozen dependency order and lifecycle; correction and reassessment records use 009's append-only commit/CAS mechanism. 015 remains the authority for source-skill compatibility and stale skill versions. The feature leaves policy authorship, check outcomes, Claim judgments, authorization, and historical interpretation with their existing owners.

## Design

### Data and behavior boundaries

- A logical window identity binds source identity, event-time boundaries, processing-time boundaries, completeness, source positions, lateness/watermark, and applicable label maturity. Each acquisition creates a distinct immutable source/execution version; a correction references its predecessor and the late arrival without replacing it.
- A period assessment compares definition, population, coverage, weighting, and freshness. Its only states are `unassessed`, `comparable`, `qualified_comparison`, and `incomparable`. It binds both exact window and definition revisions, per-dimension outcomes, reasons, and any predeclared adjustment plus interpretation limit. Missing required metadata is `unassessed`; an unresolved mismatch is never shown as an unqualified trend.
- A declared inspection policy pins planned look times, comparison family, multiplicity or sequential procedure, and stopping rule before the first relevant look. Each observed look links the policy and exposure. Undeclared looks are retained as `outcome_informed`/exploratory and cannot be relabeled independent confirmation.
- A changed bound source/window/definition revision creates an impact event before semantic review. A cycle-safe traversal follows committed dependency edges across every FR-004 type. Each dependent receives current validity `reassessment_required` or a linked reassessment; the original meaning, state, and decision remain immutable. The traversal does not use materiality or a changed-value magnitude as a gate.
- For source-skill versions, recurrence records the canonical current-validity overlay and sends the changed source facts to 015's existing compatibility/refresh logic; it does not assign or rewrite the skill lifecycle state itself.
- `recurrence.ts` is a provider for 003's fixed allowlist. It consumes the source-reuse capability and implements orientation, inspection recording, correction/invalidation hooks, and committed answer-finalization references with bounded context. It does not edit `extensions.ts` or 003 CLI/controller code.
- 009 remains the writer for run and project records. The run correction/source version is committed before project-memory refs can reference it. Same-ID/same-content recovery is idempotent; conflicting revisions are reconciled by 009; uncommitted stages stay invisible.
- Answer and trace consumers show the old and new versions, comparison/invalidation refs, and current validity. Opening a provisional answer displays completeness and lateness limits. Current validity and reproduction/availability remain separate dimensions.

### Project Structure

```text
specs/016-reconcile-recurring-windows/
├── plan.md
├── research.md
├── data-model.md
├── contracts/recurrence-v1.md
├── quickstart.md
├── tasks.md
└── execution.json

src/analytics/
├── recurrence.ts
├── invalidation.ts
└── acceptance/016.test.ts
```

**Structure Decision**: Keep all product behavior under `src/analytics/` and use the exact three source paths owned by node 016 in `docs/build/graph.json`. The plan/research/model/contract/quickstart/execution files are scoped to this feature directory. `verification.md` is produced by the final implementation unit after it runs the declared acceptance commands.

## Delivery and verification

Local graph prerequisites are 003 (registry lifecycle, answer/trace baseline, controller services) and 015 (source reuse). 009 is consumed through the existing persistence API and is not reimplemented. The sequence is window fixture and comparison, then correction traversal and hook dispatch, then full acceptance and `verification.md`. The final fixture exercises a registered `analyticsExtensionV1` through orientation, after-execution, after-evidence-commit, and pre-finalization dispatch; it asserts correction references in the answer and trace paths and validates all nine dependent types over multiple hops and two corrections.

The feature does not edit active run state or launch the product runner. Verification commands are limited to the feature test, build, and repository tests described in [quickstart.md](quickstart.md); demonstration inputs are synthetic and local.

## Complexity Tracking

No constitution violations or additional architectural projects are introduced.
