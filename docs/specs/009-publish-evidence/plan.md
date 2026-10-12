# Implementation Plan: Publish and Recover Exact Evidence

**Branch**: `009-publish-evidence` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/009-publish-evidence/spec.md`

## Summary

Implement one analytics sidecar persistence boundary for run evidence and project memory. Immutable records and artifacts are staged privately, validated against exact plan/program/environment/source/attempt bindings, appended under a scope-specific lock, and made visible by an atomic manifest revision change. Stable commit IDs make replay idempotent; changed-content reuse is rejected. Recovery trusts only the committed manifest chain and reports unreferenced material without promoting it. The same scoped append-only/CAS primitive backs run and project APIs. `stages.ts` invokes the existing neutral execution adapter for one stage; it does not start a second runner.

## Technical Context

**Language/Version**: TypeScript, ES2022, Node.js 20 or newer, strict NodeNext project configuration.

**Primary Dependencies**: The Node native test runner and existing `tsx` development dependency; the node-001 `runner-adapter.ts` and authoritative `@dag-orchestrator/neutral-execution` workspace package. No new storage or runner dependency.

**Storage**: Versioned JSON records and immutable artifact bytes in `<run>.d/analytics/` and `<project>/.dag-analytics/`; run definition/status remains in neutral run storage. Scope-specific append-only commit/revision files and a current manifest pointer form the publication boundary.

**Testing**: Native `node:test` fixtures in `src/analytics/acceptance/009.test.ts`, invoked with `node --import tsx --test`; final feature acceptance uses `pnpm test`.

**Target Platform**: Local Node.js CLI/controller runtime on supported host filesystems. The implementation must use the repository's existing atomic-write and lock capabilities and report recovery/corruption limits rather than claim cross-filesystem guarantees.

**Project Type**: Internal TypeScript analytics controller and persistence library.

**Performance Goals**: No throughput target is specified. A commit is serialized only within its run or project scope; unrelated scopes remain independent. Lock and commit operations must be bounded and return a typed conflict or recovery report rather than silently overwrite.

**Constraints**: Keep all analytics state outside `dag.run.json`; preserve append-only history; bind artifact identity by ID, revision and digest, never path alone; compare `expectedManifestRevision`; keep run and project revisions separate; reject unresolved cross-run refs; enforce current run/project write authorization and referenced-run access at publication, as well as access before returning metadata or bytes; represent missing, failed, negative, null and redacted evidence without treating process exit as support; preserve frozen API names and fields from `docs/build/interfaces.md`.

**Scale/Scope**: The feature owns `store.ts`, `publication.ts`, `stages.ts` and its acceptance fixture. It provides the first durable producer/consumer boundary for node 002 and the later inquiry controller in 003; node 011 consumes committed trace evidence, while node 019 calls project-memory persistence for catalog records.

## Constitution Check

*GATE: checked before research and re-checked after design.*

| Principle or constraint | Plan decision | Gate |
| --- | --- | --- |
| Honest analytical outcomes and execution/evidence separation | A process exit is execution metadata only. Missing or changed outputs are rejected as evidence; valid negative and failed work remains attributable. | Pass |
| Immutable history and exact evidence | Records, revisions, events and commit IDs are append-only. A new binding is a new record; replay cannot replace history. | Pass |
| Scoped project memory and rebuildability | Project and run streams have separate locks and revision heads. Project readers see only committed references; projections are rebuildable from committed history. | Pass |
| Honest access, availability and limits | Run and project commits recheck current write authorization at publication, and project writes check access to referenced run evidence. Artifact lookup resolves committed IDs and applies current grants. Redaction retains safe history and reports current availability separately from reproduction. | Pass |
| End-to-end producer, consumer, inspection and failure | `executeStageV1` is the production producer path for 002 and later 003; 011 is the first trace viewer consumer. This focused feature tests the coordinator/store contracts and seeded failures without claiming the 002/011 integrated path is already complete. | Pass |
| One neutral execution owner | `stages.ts` calls node 001's adapter/package and runs a single stage. No process runner, scheduler, or retry path is added here. | Pass |
| Accepted requirements and checks remain unchanged | The accepted feature spec, checklist, proposal thresholds, shared interface freeze, and numbered fixture criteria are read-only inputs. | Pass |

No constitution exception or additional project is required.

## Production Path, Consumers, and Trust Boundary

1. Node 002 passes its frozen synthetic computation to `executeStageV1`; node 003 later composes the same coordinator after admission. The coordinator submits one argument-vector invocation through node 001, records execution identity, validates the exact binding and declared outputs, and delegates publication to node 009.
2. `commitEvidence` validates run-scoped staged records/artifacts and advances the run analytics manifest under its expected revision. `recoverEvidence` replays only the manifest's committed chain.
3. `commitProjectMemory` and `recoverProjectMemory` apply the same mechanism to project-scoped records. At publication, the project API rechecks current write authorization and access to referenced run evidence. A project request carrying a run reference must resolve it to an already committed run manifest. No atomic transaction across the two scopes is promised.
4. Node 011 resolves evidence IDs from committed manifests, checks the current grant, and presents unavailable or redacted links safely. Node 019 consumes the project API for catalog records and rebuildable projections; it does not own a second transaction mechanism.

Workers can write only execution-specific staging outputs. Staging paths are not reader-visible. The controller is the logical writer for a scope; independent runs arbitrate project memory with the project lock and compare-and-swap revision. The neutral runner's run-file lock remains a separate concern. Paths are storage locators, not identity; the identity check uses stable IDs, revisions, digests and immutable binding refs. Local privileged access is outside the workflow's access-isolation guarantee.

## Project Structure

### Documentation (this feature)

```text
specs/009-publish-evidence/
├── spec.md                         # Accepted, read-only feature contract
├── plan.md
├── research.md
├── data-model.md
├── contracts/publication-v1.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md               # Written by the final implementation task
```

### Source Code (repository root)

```text
src/analytics/
├── store.ts                        # Scoped append-only/CAS primitive and manifest access
├── publication.ts                  # Run/project commit and recovery APIs; binding/reproduction validation
├── stages.ts                       # Single-stage coordinator over node 001 adapter
└── acceptance/
    └── 009.test.ts                 # Synthetic/local acceptance fixtures for 009 and owned T-cases
```

**Structure Decision**: Use the existing analytics namespace and the four graph-owned source outputs. Run and project state share internal storage mechanics while keeping distinct sidecar roots, locks, manifest revisions and public facades. Tests use the repository's Node native runner and do not add framework or package files.

## Compatibility and Migration Effects

The neutral run document remains unchanged. Run evidence is stored beneath `<run>.d/analytics/`; project memory is beneath `<project>/.dag-analytics/`. Existing manifests remain readable by their declared version; any future schema change adds a version or explicit migration and preserves prior bytes and refs. Node 002 can invoke the single-stage API before node 003 exists. Node 003 later composes this API instead of adding another process runner. Run and project commits are independently replayable, but no cross-scope transaction is implied.

## Complexity Tracking

No constitution violations. The shared internal CAS primitive is required to keep run and project publication semantics aligned while preserving separate scope locks and revision streams; two duplicated stores would make recovery and idempotency drift.
