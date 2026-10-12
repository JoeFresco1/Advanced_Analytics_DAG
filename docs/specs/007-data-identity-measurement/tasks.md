---
description: "Tasks for immutable data identity, denominator lineage, and descriptive measurement"
---

# Tasks: Trace Data Identity and Measurement

**Input**: Design documents from specs/007-data-identity-measurement/

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/data-measurement-v1.md, and quickstart.md.

**Tests**: Acceptance fixtures are required by the feature spec and graph task. They use synthetic/local inputs and execute the real Python calculator through the frozen dispatcher/facade path.

**Organization**: Setup and shared evidence contracts precede the two accepted user stories. User Story 2 consumes the immutable data-version model from the foundation and extends the working measurement path.

## Phase 1: Setup

**Purpose**: Build synthetic fixture helpers and test the immutable identity boundary before its implementation.

- [x] T001 Create synthetic principal/grant, source-version, transformation, and stable RecordRefV1 fixture builders plus initial identity/cardinality assertions in src/analytics/acceptance/007.test.ts.

## Phase 2: Foundational Data and Measurement Evidence

**Purpose**: Establish source/version and denominator evidence contracts required by both user stories.

- [x] T002 Implement immutable 007 source/data-version and transformation declaration/manifest validators in src/analytics/data.ts; bind exact shared refs, keep source role separate from access role, require declared grain/cardinality before new transformations, and preserve unknown historical metadata.
- [x] T003 Implement denominator-lineage and MeasurementFindingV1 validation in src/analytics/measurement.ts; bind ordered transformation refs, reconcile supplied row/entity counts, and expose exact support blockers without assigning global Claim or presentation state.

**Checkpoint**: Source, grant, transformation, denominator, and unknown metadata remain separately attributable; no numeric summary is calculated in TypeScript.

## Phase 3: User Story 1 - Know which data was measured (Priority: P1)

**Goal**: Trace a displayed descriptive denominator to exact source versions, population/period, transformations, grain, joins, missingness, exclusions, units, and group meaning.

**Independent Test**: Run the fixed synthetic customer fixture through the actual Python descriptive calculator and inspect its returned Result scope and denominator lineage. A declared one-to-many join remains usable; duplicate-key expansion and an unexplained population filter expose their owning finding and block support for the affected measurement.

- [x] T004 Add test-first real-computation cases in src/analytics/acceptance/007.test.ts for count, sum, mean, proportion, units, population/period, group scope, missingness/exclusions, denominator lineage, and an undefined zero-denominator proportion. The test must invoke the real Python calculator and must not supply a fabricated result payload.
- [x] T005 Implement authoritative count, sum, mean, and proportion calculations plus observed row/entity/group and missingness/exclusion counts in python/analytics_worker/descriptive.py using the frozen 001 DescriptiveRequestV1/DescriptiveResultV1 contracts; preserve denominator zero with an absent value and reason for undefined statistics.
- [x] T006 Implement the descriptive TypeScript facade in src/analytics/descriptive.ts; invoke the shared computeAnalytics/009 stage path with exact plan, source, transformation, program, environment, and execution bindings, validate the echoed result, and return Python values without calculating summaries.
- [x] T007 Complete denominator reconciliation and support-blocking measurement findings in src/analytics/measurement.ts; distinguish justified row multiplication from unexplained entity/cardinality changes and hidden exclusions, retaining exact expected/observed evidence and denominator refs.

**Checkpoint**: A Result resolves to an exact input and transformation path. A valid declared grain expansion passes; duplicate joins and hidden exclusions remain visible and block affected support.

## Phase 4: User Story 2 - Compare acquisitions (Priority: P2)

**Goal**: Show whether two acquisitions can be replayed and compared when content, source guarantees, or semantic meaning changes.

**Independent Test**: Compare synthetic acquisitions with identical schema but changed units, timezone, key meaning, or category definition; compare a query-time-only acquisition; and inspect a late-event/immature-label fixture. Unknown, stale, or mismatched scope remains explicit and blocks only the dependent claim.

- [x] T008 Add test-first acquisition and semantic-drift cases in src/analytics/acceptance/007.test.ts for unknown legacy grain/cardinality, same-schema unit/timezone/key/category changes, query time without repeatable snapshot, missing group meaning, freshness/watermark drift, late events, immature labels, and source-role/grant mismatch.
- [x] T009 Extend src/analytics/data.ts with acquisition scope/time/consistency and replay availability, semantic-definition versions independent of schema fingerprint, source role/grant matching through 001, explicit unknown legacy facts, and new-version/correction links for changed meaning or late data.
- [x] T010 Extend src/analytics/measurement.ts with declared freshness and comparison assessment, event-time/processing-time/watermark evidence, target maturity counts and exclusions, group-meaning checks, and exact support blockers for semantically incomparable or immature measurements. Do not introduce a global freshness threshold or infer category meaning.

**Checkpoint**: A query timestamp alone is not an exact snapshot; changed semantics and immature labels keep their counts and evidence visible and cannot support an unqualified affected claim.

## Phase 5: Polish and Cross-Cutting Acceptance

**Purpose**: Run the complete feature acceptance and record actual evidence without changing the frozen requirements or fixtures.

- [X] T011 Complete the FR-001–FR-007, SC-001–SC-008, all four feature Edge Cases, graph-required missing-group/source-role fixtures, and proposal T05/T08 assertions in src/analytics/acceptance/007.test.ts; run pnpm build, pnpm test, and the focused 007 acceptance command; write actual outcomes, limits, and FR/SC/Edge-to-evidence links in specs/007-data-identity-measurement/verification.md.

## Dependencies and Execution Order

### Phase Dependencies

- **Setup**: T001 creates only synthetic/local fixture helpers and initial identity assertions.
- **Foundation**: T002 defines exact immutable data and transformation records; T003 consumes those definitions for denominator-lineage and finding validation. This blocks both stories.
- **User Story 1**: T004 adds its actual-computation cases before T005/T006 implement the authoritative calculator and facade; T007 completes the consumer-facing denominator findings after both calculation paths exist.
- **User Story 2**: T008 adds semantic/acquisition cases first. T009 extends source records before T010 consumes those fields for measurement validity. It depends on the User Story 1 measurement/result path.
- **Final verification**: T011 follows all previous units and records only command results actually observed by the implementation worker.

### User Story Dependencies

- **US1 (P1)**: Depends on the shared foundation and delivers the fixed descriptive path with denominator reconciliation.
- **US2 (P2)**: Depends on the immutable identity and descriptive result path; it adds acquisition comparison, semantic drift, freshness, access-role, and label-maturity behavior.

### Parallel Opportunities

The planned sequence has no safe task-level parallel edits: acceptance tasks and implementations intentionally modify the same feature test file, and measurement validation consumes the data model before it can compile. The compiler may run independent features concurrently in isolated worktrees; 007 units themselves stay ordered to protect their shared files.

## Implementation Strategy

### MVP First

Complete T001–T007 and validate User Story 1 independently: exact source/transform trace, authoritative descriptive values, denominator reconciliation, allowed one-to-many expansion, and blocked support for duplicate joins or unexplained exclusions.

### Incremental Delivery

1. Establish immutable source/transformation and denominator evidence.
2. Deliver the real descriptive Python computation and TypeScript binding facade.
3. Add acquisition replayability, same-schema semantic drift, freshness, source-role access, group meaning, and label maturity.
4. Run final feature acceptance and write verification.md with actual evidence.

## Task-to-Unit Ownership

The execution manifest assigns each task exactly once. Identity/data and denominator contracts belong to the foundation unit; descriptive computation, facade, and P1 fixtures belong to the descriptive unit; acquisition/semantic checks belong to the P2 unit; only the final acceptance unit writes verification.md.


## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.

## join-evidence-correction

- [ ] T012 Extend the existing 007 transformation producer with optional typed declared join and observed join evidence; historical absent fields remain unavailable and never imply a known join. Export validateTransformationJoinV1(value, inputVersionRefs). A known join has joinId, kind (inner/left/right/full), and nonempty keyMappings of leftVersionRef/leftKey/rightVersionRef/rightKey. Exact refs must belong to distinct declared input versions; key strings must be nonempty; duplicate mappings, unknown keys and kinds are invalid. Add optional join to TransformationDeclarationV1 and TransformationObservationsV1; validate known values via the exported helper and retain observed mismatches as evidence, not a fabricated pass. Existing legacy fixtures must still work. Keep immutable digest validation including the optional fields; do not rewrite old committed records. Add tests through both real declaration/manifest validators for a valid two-input join, foreign ref, empty mapping, invalid kind, duplicate mapping, tampered digest, observed different join and absent legacy evidence. Run the external analytics-join-probe.mjs through tsx plus the whole existing 007 test file. Record that this is a corrective successor to accepted 007 T002, not a relabeling of its historical acceptance.

## Capability amendment — 2026-10-10

The execution manifest owns amended unit order and review scope. The preserved original task text remains a whole-feature obligation; split units jointly satisfy it. New corrections have their own checklist IDs.
