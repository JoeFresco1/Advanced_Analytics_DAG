# Tasks: Numerical report bindings and browser evidence trace

**Input**: Design documents in specs/011-inspect-result-trace/  
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, and
contracts/http.md  
**Dependencies**: Feature 009 committed evidence and Feature 010 frozen checks
are graph prerequisites.

## Phase 1: Setup

The existing TypeScript build, Node test runner, HTTP server, and headless
Edge/CDP pattern are sufficient. Do not add dependencies or change shared
tooling.

## Phase 2: Foundational

**Purpose**: Establish synthetic committed-record fixtures shared by the
answer, trace, linked-provenance, access, and browser acceptance paths.

- [ ] T001 Create deterministic committed Claim, Result, CheckResult, Plan, Execution, transformation, source-version, artifact, and grant fixtures, plus typed RecordRefV1 links for source version, skill version/status, independent validation, authority decision, supplied reference, executed recipe, and outcome exposure; include paired workflow-readable and independently permissioned source fixtures and fixture assertions for both SC-008 labels (`workflow-level` and `independently permissioned source`) in src/analytics/acceptance/011.test.ts

**Checkpoint**: Fixtures use only synthetic/local data and retain stable
EvidenceRef revisions and digests.

## Phase 3: User Story 1 - Follow a reported number (Priority: P1)

**Goal**: Bind every displayed number to an exact committed Result and allow
the browser to follow its Claim through checks and computation to the data
version.

**Independent Test**: Start the production analytics handler with committed
synthetic records, open the inquiry shell in a browser, click a report value,
and reach the matching data-version trace. A planted selector, value, unit,
denominator, rounding, or scope mismatch is visibly unsupported. Missing or
changed evidence remains visible.

- [ ] T002 Add T11 cases that independently swap metric values, units, and denominators, plus rounding/scope mismatches and missing/changed/redacted EvidenceRefs, and assert each incorrect assertion is blocked before supported display in src/analytics/acceptance/011.test.ts
- [ ] T003 Add real HTTP and headless-browser acceptance through the production answer/trace URL builders: request both shell routes and JSON APIs against committed synthetic linked-provenance records; click a report value through the bound Claim and Result to its committed data-version entry; verify exact RecordRefV1 links for source, skill/status, validation, authority, supplied-reference, executed-recipe, and outcome-exposure are present in the answer/trace projection in src/analytics/acceptance/011.test.ts
- [ ] T004 Implement ReportAssertion and ReportChart binding validation, derived displayed values, current check eligibility, ordered evidence trace assembly, and generic linked-provenance projection with exact committed RecordRefV1 identity and safe missing/changed/restricted link conditions in src/analytics/report.ts
- [ ] T005 Implement the mountable production HTTP handler, answer/trace JSON APIs, inquiry/claim shell routes, artifact-ID route dispatch, and stable answer/trace URL builders; resolve every generic provenance ref by exact ID/revision/digest and apply current per-scope grants before metadata in src/analytics/http.ts
- [ ] T006 Implement the answer-first viewer and value/chart selection that opens the bound claim trace; display permitted generic provenance refs/status as escaped text and keep the committed data-version entry reachable in viewer/analytics.html

**Checkpoint**: The inquiry and claim page routes return the generic shell;
the real handler returns committed answer/trace data; browser selection ends at
the bound source-version entry.

## Phase 4: User Story 2 - Understand the answer (Priority: P2)

**Goal**: Lead with an honest scoped answer, uncertainty, and limitations,
then reveal chart choices and runtime lineage on selection while applying
current access scope to every evidence view.

**Independent Test**: Each omitted required first-screen field makes the
report incomplete. Negative/inconclusive results retain their interpretation,
and the browser opens every chart in the fixture and asserts its full bound
trace: aggregation, axes and labels, scale, filters, uncertainty
definition/encoding, and exact source Result. A metadata-only Principal
receives only scoped metadata; denied raw reads and seeded markup/path attacks
reveal no protected bytes or executable actions.

- [ ] T007 Add an independent omission case for answer, population, period, material assumptions, uncertainty, and limitations, plus negative/inconclusive and causal-wording cases; for every fixture chart, use browser assertions for aggregation, axes/selectors/units, labels, scale/range, filters/population effect, uncertainty definition/encoding, and exact source Result ref, including planted aggregation/scale/filter changes, in src/analytics/acceptance/011.test.ts
- [ ] T008 Implement first-screen completeness, structured Claim interpretation labels, and exact chart binding assessment for aggregation, axes/selectors/units, scale/range, filters/population effect, labels, uncertainty definition/encoding, and source Result ref in src/analytics/report.ts
- [ ] T009 Render completeness and limitations on the first screen and render each chart's complete bound trace, including permitted source Result ref, aggregation, axes, labels, scale, filters, and uncertainty meaning, as browser-visible fields for T007 assertions in viewer/analytics.html

- [ ] T010 Add T60 active-markup/out-of-root-path cases and metadata-only, denied raw access, grant revocation, traversal, and symlink-escape cases; exercise paired workflow-readable and independently permissioned source fixtures through real answer/trace endpoints and browser assertions for both SC-008 labels (`workflow-level` and `independently permissioned source`), with zero ungranted raw values or restricted paths in either viewer response, in src/analytics/acceptance/011.test.ts
- [ ] T011 Enforce current grant checks before metadata or byte responses and for every linked provenance ref; resolve exact committed record/artifact IDs and ID/revision/digest bindings, preserve missing/changed/redacted/restricted safe labels without raw/protected content, canonicalize within approved roots, reject traversal/symlink escape, and return safe errors in src/analytics/http.ts
- [ ] T012 Render report/source text and linked-provenance labels/refs as text nodes, keep missing/restricted/unsafe evidence failures visible, and expose the paired SC-008 boundary labels (`workflow-level` and `independently permissioned source`) in viewer/analytics.html for T010 browser assertions against their matching fixtures without ungranted raw values or restricted paths

**Checkpoint**: Metadata and bytes are separately authorized; no unauthorized
values or restricted paths leave the handler; omission blocks report
completeness; charts retain their bound source details; and unsafe content
cannot execute in the browser.

## Phase 5: Polish and cross-cutting verification

**Purpose**: Run the complete feature acceptance and record actual outcomes.

- [ ] T013 Run pnpm build and pnpm test, then write actual FR/SC/edge/T11/T60 outcomes and evidence references to specs/011-inspect-result-trace/verification.md

## Dependencies and execution order

### Phase dependencies

- Setup needs no change.
- Foundational synthetic fixtures precede each story.
- User Story 1 establishes the binding and browser trace used by Story 2.
- User Story 2 adds completeness and chart explanation to the same report.
- Final verification follows all stories and records real evidence.

### User-story dependencies

- US1 depends on T001 and Features 009/010.
- US2 depends on US1's report, trace API, and viewer path.
- The task-generation manifest serializes this feature's units to protect
  shared report, handler, viewer, and acceptance files.

### Parallel opportunities

No checklist tasks are marked parallel: each story changes the same acceptance
file and/or the report, handler, or viewer modules used by earlier tasks.
Separate file edits can be reviewed independently, but execution units remain
ordered as required by the manifest contract.

## Implementation strategy

### MVP

Complete T001-T006 first. This produces the minimum answer-first report,
numerical binding, generic inquiry and claim routes, and browser click-through
for a committed synthetic Result. Validate this path before adding narrative
completeness, chart detail, and access adversaries.

### Incremental delivery

1. Add the P1 exact assertion-to-data-version trace.
2. Add the P2 first-screen completeness and chart trace.
3. Add the P2 grant, markup, and path-boundary behavior as part of the
   answer-understanding journey.
4. Run full feature acceptance and create verification.md with actual results.

## Requirement and acceptance coverage

The execution manifest maps each FR-001 through FR-008, SC-001 through
SC-010, and EC-001 through EC-003 to one or more checklist tasks. FR-003 and
SC-001/SC-005/SC-008 include the generic linked-provenance and paired-boundary
read path. EC IDs follow the three Edge Cases bullets in spec.md order.
T001-T006 cover the primary T11 numerical binding case, including explicit
unit and denominator swaps. T007-T009 specify browser assertions and rendering
for every chart's complete bound trace. T010-T012 cover T60 unsafe content,
out-of-root paths, and paired SC-008 boundary labels. Missing/redacted
evidence also exercises T22 as supporting evidence; 009 remains its primary
owner. T001/T003/T010/T012 cover the synthetic committed linked-provenance
answer and trace path before 014/022 are built.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
