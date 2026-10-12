# Implementation Plan: Execute and Inspect Evidence

**Branch**: `002-executable-evidence` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Accepted Feature Specification for Execute and Inspect Evidence; shared interface freeze and build graph.

## Summary

Deliver the first fixed-analysis user path across the feature 009 execution/publication coordinator, feature 010 frozen required-check policy, and feature 011 report and browser trace. A local synthetic Python fixture produces a deterministic baseline/candidate comparison, including an observed no-gain case. The demo binds a typed immutable Plan and exact program, environment, source, transformation, and output references, invokes the computation through 009 executeStageV1, publishes through 009 commitEvidence, evaluates exact checks through 010, and opens the committed report through 011 HTTP/viewer behavior. The 002 acceptance test drives that route in a feature-local CDP browser harness implemented in `src/analytics/acceptance/002.test.ts`. Failure variants cover overlap, malformed or absent check output, zero-exit missing result, altered artifact, same-revision publication race, interruption and replay, and inaccessible or redacted evidence. A valid negative or inconclusive observation remains committed without a method retry.

The demo is a fixed-scope integration caller. It does not create a second scheduler, publication mechanism, policy, state vocabulary, analytics controller, or general predictive calculator. It does not depend on the later 003 inquiry controller or 007 descriptive lineage/calculator. Its single-stage execution uses the exact production coordinator that 003 will compose.

## Technical Context

**Language/Version**: TypeScript on the repository's Node.js >=20 target; Python version is the exact version pinned by feature 001 in python/pyproject.toml and uv.lock and recorded in the computation binding.

**Primary Dependencies**: Existing pnpm TypeScript toolchain and feature 001's neutral execution workspace package, Pydantic computation/Plan contracts, generated JSON schemas, conformance vectors, and Python package. Consume 009 stages/publication/recovery, 010 checks/policy, 011 report/HTTP/viewer, and the 18-bootstrap fixture registry. Do not add dependencies or edit shared manifests.

**Storage**: 009-owned run sidecar under <run>.d/analytics with append-only records, immutable artifacts, manifest revision, commit ID, and execution staging. The viewer resolves committed IDs through 011 and current access rules. Do not edit or extend dag.run.json.

**Testing**: Existing TypeScript node:test suite through pnpm test; Python tests through uv run --project python pytest; structural requirement audit through python scripts/audit_spec_coverage.py. The 002 acceptance test drives the synthetic producer, real stage/publication/check facades, and 011 browser viewer path. Its test-local CDP harness launches an installed Edge or Chrome, using `DAG_ANALYTICS_BROWSER_PATH` when set and documented platform locations otherwise; it uses no Playwright/Puppeteer package and fails if no browser can start. The acceptance command, browser/version, route, visible trace observation, and any captured browser evidence are recorded in verification.md only after the acceptance run.

**Target Platform**: Local development and test environment with the repository-supported Node and Python runtimes. All example data and sources are synthetic and local. Viewer evidence must label any workflow-level access boundary accurately.

**Project Type**: TypeScript controller/API and browser viewer with a Python synthetic calculation fixture.

**Performance Goals**: Complete the small deterministic fixture within the declared stage timeout. This feature adds no throughput, latency, reliability, or release threshold beyond the accepted fixture outcomes.

**Constraints**: Preserve the accepted 002, 009, 010, and 011 specifications, policy, reference outputs, and checks. Respect graph file ownership. Use only feature outputs plus declared prerequisite APIs. Do not import 003/007 implementation paths, run a second runner, weaken frozen policy, rewrite history, or convert analytical outcomes to execution failures/retries. Do not claim the local fixture is isolated from a worker with shell access.

**Scale/Scope**: One fixed synthetic computation and a bounded scenario matrix; one result trace with exact evidence links; no live worker, external source, generalized model fitting, or project-wide analytics workflow.

## Constitution Check

### Pre-design gate

- **Honest answer and bounded scope**: The fixed calculation exposes its observed comparison, scope, uncertainty/limitations, and exact evidence. A no-gain or inconclusive result is retained as a valid result; it does not request a favorable retry.
- **Execution differs from evidence**: Stage process status, structured check outcome, claim evidence state, supported-presentation eligibility, publication status, availability, and reproduction remain separately represented using the canonical contracts.
- **History and policy remain fixed**: Frozen Plan/policy refs and content digests are passed through the execution and report. Failed, malformed, missing, stale, conflicted, and inaccessible evidence stays inspectable. This slice does not permit worker policy edits or rewrite a committed revision.
- **Access and judgment attribution**: The viewer checks the current fixture principal/grant. Denied access returns only the metadata allowed by 001/011. No local shell isolation claim is made.
- **End-to-end path**: Python producer -> 009 stage and publication -> 010 required checks/eligibility -> 011 browser trace, with seeded failure and recovery fixtures.
- **Shared ownership**: The implementation changes only 002's three code outputs and verification.md; it consumes rather than edits focused-feature implementations, shared interfaces, manifests, accepted specs, fixture lock, or build graph.

**Gate result**: Pass. No constitution exception or contract ambiguity is introduced by this plan.

## Design Decisions

See [research.md](research.md) for the decision log and alternatives. The Plan payload is an immutable fixture instance of the existing Pydantic PredictivePlanV1 member of PlanRegistrationV1, validated using the schemas generated by 001. The demo records a Plan ref and digest; it does not implement 008 registration or add an interpretation state. The Python program uses the existing mode-specific request/result contract for this fixed synthetic case and owns no reusable predictive facade. Its only execution path is 009 executeStageV1 with explicit argv and bounded timeout. The controller records and publishes exact output bindings before the browser presents a claim. Required check output and eligibility are then assessed by 010 against the same committed artifact digest and frozen policy. A failed check remains an inspectable result and blocks supported presentation.

## Phase 0: Research Summary

Internal contract review resolved the implementation choices without open questions. The product proposal, constitution, canonical vocabularies, access/judgment contract, frozen interfaces, shared architecture, feature 002 specification, focused 009/010/011 specifications, and graph ownership establish the contracts and existing commands. No external research or new dependency is required. See [research.md](research.md).

## Phase 1: Design Summary

- Data model and immutable bindings: [data-model.md](data-model.md).
- Demo command, scenario IDs, producer/controller/viewer boundaries, and error outcomes: [contracts/evidence-demo-v1.md](contracts/evidence-demo-v1.md).
- Runnable validation sequence: [quickstart.md](quickstart.md).
- User-visible behavior follows the three accepted P1 stories in [spec.md](spec.md).

### Constitution re-check

Pass. The design carries the established 009/010/011 identities and states without renaming, shadow policy, or custom commit semantics. It records negative and failed observations; it uses current grants; it includes a producer, production caller, viewer, failure injection, and recovery. The fixed Plan and scenario data are local synthetic fixtures; no reference labels or answer keys are added to worker context. No accepted criteria or thresholds are changed.

## Project Structure

### Documentation (this feature)

    specs/002-executable-evidence/
    ├── plan.md
    ├── research.md
    ├── data-model.md
    ├── contracts/evidence-demo-v1.md
    ├── quickstart.md
    ├── tasks.md
    ├── execution.json
    └── verification.md (written by final implementation/acceptance unit)

### Feature-owned source outputs

    python/analytics_worker/demo_evidence.py
    src/analytics/demo-evidence.ts
    src/analytics/acceptance/002.test.ts

Feature 002 consumes 009 store/stage/publication/recovery, 010 policies/checks, 011 report/HTTP/viewer, and 18-bootstrap fixtures. It does not edit those owners' files. It does not edit dag.run.json, package manifests, shared architecture/interfaces/graph, accepted specifications, or another feature's planning folder.

**Structure Decision**: Keep the runnable demo facade and acceptance test in the exact graph-owned TypeScript paths. Keep the deterministic synthetic calculation in the graph-owned Python worker path. Keep all design and task artifacts, including the integration contract, inside this feature directory.

## Acceptance Trace

Primary criteria remain those in spec.md and the accepted verification maps. Implementation must provide evidence for all FR-001–FR-010, SC-001–SC-008, and the six Edge Cases in their source order. Integrated proposal cases exercised by this fixed caller are T01, T02, T03, T04, T10, T13, T14, T15, T22, and T52. T23 and T60 remain exercised by their focused 010/011 acceptance suites; 002 consumes the resulting frozen check and viewer behavior without taking primary ownership, as recorded in docs/build/acceptance-plan.md. The final report records the exact demo and test commands, real command output, committed IDs/revisions, installed browser and version, the 011 route opened by the CDP harness, visible page/trace evidence, exit codes, and any environment limitation; it does not claim acceptance based on planning or process exit alone.

The foundation unit runs `pnpm build` followed by `node --test dist/analytics/acceptance/002.test.js`. At that checkpoint, the acceptance file contains the `stage-call` case that passes the frozen synthetic Plan and Python fixture through 009 `executeStageV1` and asserts the returned execution/output binding before check or viewer integration proceeds.
