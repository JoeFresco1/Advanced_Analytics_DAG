# Research: Recurring Windows, Comparability, and Invalidation

This is bounded repository-contract research. No external research is needed: the accepted feature spec, proposal, canonical state vocabulary, shared interface freeze, build architecture, and dependency graph settle the engineering choices.

## Decision 1: Separate a logical window identity from each immutable window version

**Decision**: Derive a stable logical `windowKey` from the source identity and full window boundary semantics; assign every acquisition/correction its own source version, execution ref, sequence, and immutable `WindowVersionV1`. A correction includes `correctsWindowRef` and arrival provenance.

**Rationale**: FR-001 and FR-002 require a new source and execution version per recurrence while retaining the same bounded-window identity where a late event corrects it. Proposal §14 says recurrence cannot overwrite the prior measurement and late-data corrections retain their arrival history. 009 binds exact source/execution versions and commits immutable records.

**Alternatives considered**: Use the source path or schema fingerprint as identity (does not represent boundaries/completeness/source position); replace a mutable “latest window” record (rewrites historical meaning); treat each correction as a new unrelated logical window (loses correction ancestry).

## Decision 2: Use the existing four-state period comparison and preserve dimension reasons

**Decision**: Return only `unassessed`, `comparable`, `qualified_comparison`, or `incomparable`. Store exact window/definition refs, explicit per-dimension match/difference/unknown assessments, reason codes with supporting refs, and any predeclared adjustment and interpretation limit.

**Rationale**: `state-vocabularies.md` defines these states and makes missing required metadata `unassessed`. A bounded documented adjustment permits `qualified_comparison`; it must not be presented as an unqualified trend. FR-003 names definition, population, coverage, weighting, and freshness.

**Alternatives considered**: Add a feature-local comparison state (would fork the canonical vocabulary); reduce comparison to a Boolean or one score (would hide the reason and adjustment); infer comparability from output magnitude (would make the judgment outcome-dependent).

## Decision 3: Preserve event-time and processing-time bounds and their separate provenance

**Decision**: Store both clocks, source positions, watermark/completeness, lateness, and label maturity in every window identity. Route correction impact by event-time membership while retaining processing-time arrival history.

**Rationale**: Proposal §6 and FR-007 require both time domains and correction impact on the event-time window. FR-002 and SC-006 require the remaining identity dimensions; SC-005 tests delayed arrival.

**Alternatives considered**: Use processing time as the window key (would target the wrong historical window); retain only event time (would erase when the system learned of the event); treat unknown maturity/watermark as complete (would invent metadata).

## Decision 4: Treat monitoring looks as planned, attributable exposure events

**Decision**: Pin the schedule, comparison family, multiplicity/sequential procedure, and stopping rule before the first relevant look. Record every look against that policy. An undeclared look is exposure and outcome-informed exploratory work; it cannot retain an independent confirmatory label. It is never retried to seek a favorable finding.

**Rationale**: FR-006, canonical plan-exposure states, proposal §§7–8/14, and T58 require this. Orientation carries prior exposure; post-execution registers a look before interpretation/finalization.

**Alternatives considered**: Permit unrestricted repeated fixed-significance testing; call a new session a new confirmation; count only favorable looks; apply infrastructure retry semantics to an analytical look. Each would erase exposure or bias interpretation.

## Decision 5: Traverse committed dependency records and invalidate before semantic reassessment

**Decision**: Read exact bound dependency refs from committed 009 records and perform a cycle-safe O(V+E) traversal across Results, Claims, Check Results, reports, answer cards, ledger entries, source-skill versions, Use Assessments, and Decision references. Emit an append-only impact record; set each changed-bound dependent to `reassessment_required` before a reviewer/worker judgment. A later judgment can resolve the current assessment but cannot remove the invalidation event.

**Rationale**: FR-004, canonical current validity, I05/I07/I08/I09/I11/I12/I17/I19, and proposal §14 establish exact traversal and immutable history. The nine record kinds are a frozen contract. 009 owns commit/CAS/recovery; 015 owns its source-skill compatibility result.

**Alternatives considered**: Traverse only Results and Claims (misses user-visible and memory consequences); stop after one dependency hop; suppress “immaterial” changes; mark history in place; create a second graph store. These conflict with accepted requirements or duplicate 009.

## Decision 6: Export one `recurrence` provider and exercise 003's real registry lifecycle

**Decision**: Export `analyticsExtensionV1` from `recurrence.ts` with `extensionId: "recurrence"`, dependency `source-reuse`, and bounded orientation/inspection/correction/finalization hooks. Use the fixed loader and dependency-ordered dispatch owned by 003. Keep registry source and CLI/controller edits out of 016.

**Rationale**: `interfaces.md` assigns this provider and exact dependency, defines hook order and failure handling, and identifies 003 as sole owner of the lifecycle. The graph requires 016 to provide real consumers.

**Alternatives considered**: Register by arbitrary filesystem discovery; call recurrence functions only from a unit test; edit 003 shared files from 016. These bypass the frozen capability boundary or violate graph ownership. The acceptance fixture will load the provider through the production registry seam and assert that orientation, afterExecution, afterEvidenceCommit, and beforeFinalize dispatch and return committed refs.

## Decision 7: Reuse 009 persistence for correction records and answer/trace bindings

**Decision**: Commit run-scoped corrected source/window/execution records first, then publish project-scoped impact and current-validity records using 009 `commitEvidence`/`commitProjectMemory` and their frozen refs/CAS semantics. Answer/trace outputs point to these refs; no direct writes, second store, or `dag.run.json` field is added.

**Rationale**: Shared architecture and `interfaces.md` reserve one authoritative writer and append-only commit/recovery semantics to 009. The graph-boundary acceptance path requires answer/trace visibility.

**Alternatives considered**: Mutate committed result or card records; store current-validity only in a non-durable cache; write directly beside a run; create a recurrence-owned project catalog. Each would lose history or fork the persistence contract.

## Decisions resolved

No unknowns remain. Tests and demonstrations use synthetic/local fixtures. Product execution, reference-answer modification, policy changes, thresholds, and accepted-spec edits are outside this feature plan.
