# Implementation Plan: Trace Data Identity and Measurement

**Branch**: 007-data-identity-measurement | **Date**: 2026-09-28 | **Spec**: spec.md

**Input**: Feature specification at specs/007-data-identity-measurement/spec.md

## Summary

Implement immutable data-version and transformation provenance in TypeScript, then use that provenance to validate the population and denominator attached to authoritative descriptive results. The Python calculator computes count, sum, mean, and proportion from the admitted inputs. The TypeScript facade binds the exact plan, source versions, transformations, program, environment, and execution, and validates the returned scope and lineage without calculating or inventing a numerical summary.

Every acquisition identifies its source, declared source role, principal grant reference, retrieval scope and time, source consistency evidence, semantic definitions, and replay/availability facts. Every transformation records its exact input and output versions, declared grain and join cardinality, entity keys, observed row and entity counts, exclusions, joins, and time boundaries. Unknown historical metadata remains explicit. A denominator is traceable from its source population through each transformation, including justified row expansion and reconciled exclusions. Integrity findings identify their owning measurement check and provide support-blocking evidence for the dependent claim consumer.

## Technical Context

**Languages/Runtime**: TypeScript in the existing analytics package; Python in the uv-locked analytics worker. Use the versions and package lock established by feature 001.

**Primary Dependencies**: Frozen RecordRefV1, Principal, SourceGrant, PlanRegistrationV1, AnalyticsRequestV1 and AnalyticsResultV1 contracts; 001 Python dispatcher and generated schemas; 009 executeStageV1 and publication interfaces; 18-bootstrap synthetic fixture and reference-evaluator interfaces.

**Storage**: Immutable versioned records and artifacts in the 009 analytics sidecar and commit service. This feature adds no state to dag.run.json and no independent store or index.

**Testing**: Feature acceptance in src/analytics/acceptance/007.test.ts. The fixture runs the real Python descriptive calculator through the shared request/dispatcher path and the TypeScript facade. Planned repository checks are pnpm build, pnpm test, and the focused 007 acceptance file.

**Target Platform**: Local analytics controller and bounded Python worker for synthetic or local tabular inputs.

**Project Type**: Analytics controller and computation package, not a new runner, service, database, or viewer.

**Performance Goals**: No new throughput target is required for bounded fixtures. Preserve the shared inquiry and command budgets; reject unbounded reads and do not use input size to weaken lineage checks.

**Constraints**: Feature 001 owns the normative wire schema, common dispatcher, dependency lock, principal/grant enforcement, and computation binding. Feature 009 owns execution, commit, and recovery. Feature 003 selects this calculator; 011 renders the evidence trace. Feature 007 must not edit those shared owners, accepted specifications/checklists, policy thresholds, fixture lock/reference answers, package manifests, or another feature. TypeScript validates bindings and supplied evidence only; Python computes every descriptive value and numerical count. Unknown or missing metadata cannot become zero, a pass, a repeatable snapshot, or an exact comparison.

## Constitution Check

### Before design

- Honest answer and inspectable numerical claims: pass. The feature gives each result its population, period, units, denominator and exact input lineage.
- Execution is separate from evidence and claim support: pass. Process completion does not resolve a measurement finding; support blockers are explicit evidence consumed by the controller.
- Immutable history and explicit unknowns: pass. Changed source meaning or transformation creates a new reference; unknown legacy grain/cardinality and unknown consistency remain unknown.
- Scoped access and accurate guarantees: pass. Access is checked through 001 Principal/SourceGrant and the actual source role is recorded; no filesystem isolation claim is added.
- End-to-end path and failure evidence: pass. Real Python results flow through the TypeScript facade, and synthetic faults expose their owner check and support impact.

### After design

The design uses only graph-owned 007 output files and frozen shared interfaces. It does not alter the approved feature contract, redefine the global CheckResult or supported_presentation_eligible policy, create a second runner, or produce values in TypeScript. The measurement service returns exact findings and support blockers for 003/010 to consume; canonical Claim and inquiry states remain owned by their existing contracts. No constitution exception is required.

## Design and Runtime Boundaries

1. 001 validates the registered descriptive plan, authenticated principal, source grants, common computation binding, and strict Pydantic payload. 009 admits and executes the declared Python stage and commits exact evidence.
2. data.ts defines immutable 007 source-version and transformation evidence, validates references, expected source role, grain, key/cardinality declarations, and access evidence, and preserves source meaning separately from schema identity. Source reads call the 001 grant boundary; 007 never creates a grant.
3. descriptive.py consumes the 001 DescriptiveRequestV1 and computes the requested statistics and diagnostic counts. It returns values, missingness/exclusion counts, units, group and period scope, and denominator evidence. With a zero denominator it records denominator zero and omits the undefined numeric value with a reason; it never substitutes zero for an undefined mean or proportion.
4. measurement.ts reconciles the Python-produced counts against immutable transformation declarations and exact denominator definitions. It records a named check finding with subjects, expected/observed evidence, rationale, and whether it blocks support. It does not recalculate reported metrics, issue global CheckResultV1 records, or assign a Claim evidence state.
5. descriptive.ts exports the mode facade used by controller 003. It supplies exact source and transformation refs to computeAnalytics/executeStageV1, validates the returned binding and scope, and exposes the Python-produced result and 007 findings. It contains no statistical formula or fabricated fixture summary.
6. Controller 003 consumes the facade; its existing check/presentation path treats a 007 support blocker as a required measurement condition. Feature 010 remains the sole owner of policy-bound supported_presentation_eligible. Feature 011 can follow the Result through its source and transformation refs without 007 changing the viewer.

A change of unit, category meaning, key meaning, timezone, population definition, or freshness/maturity rule creates new semantic evidence even when the schema fingerprint is unchanged. Old records remain inspectable and dependent current evidence requires reassessment. Comparisons require matching known semantic definitions or an explicit, versioned transformation; no implicit currency, timezone, category, or key conversion is allowed.

A declared one-to-many join may increase rows while preserving the customer denominator when its expected cardinality, output grain, distinct-entity counts, and resulting denominator all reconcile. An undeclared or over-cardinality join, hidden exclusion, missing group definition, immature required label, or source-role/grant mismatch returns a finding tied to the affected subjects. The finding remains visible and blocks support for the affected measurement; unrelated measurements retain their own scope.

## Acceptance Coverage

| Contract | Observable evidence |
| --- | --- |
| FR-001, SC-006 | Each acquisition fixture binds source, declared role, grant, retrieval scope/time, consistency evidence, and exact data version or explicit unknown/unavailable replay. |
| FR-002, FR-004, SC-001, SC-002 | Transformation inputs/outputs, predeclared grain/cardinality, entity and row counts, joins, exclusions, and cutoffs reconcile to each result denominator. Duplicate joins and hidden exclusions identify their check and block affected support; a validated one-to-many join remains usable. |
| FR-003, SC-001 | Returned result binds statistic and value to units, population, period, missingness, exclusions, denominator definition, and full transformation path. |
| FR-005, SC-003, FR-007, SC-005 | A query-time-only acquisition is never called an exact snapshot. Legacy cardinality stays unknown until a consuming transformation declares and validates its expected input grain/key counts. |
| FR-006, SC-004, SC-007 | Same-schema unit, timezone, key, or category meaning changes have distinct semantic revisions and cannot be compared as unchanged. |
| SC-008 | Late events keep event-time/source provenance; an immature label is excluded or marked unavailable with its cohort count and cannot silently support an ineligible population. |
| EC-001 | A declared valid one-to-many join increases output rows without changing the distinct-customer denominator and remains usable. |
| EC-002 | Currency, timezone, key, and category-definition revisions stay visible; no unversioned conversion or comparison is made. |
| EC-003 | Query time without repeatable source consistency is explicit and does not receive an exact-snapshot label. |
| EC-004 and proposal T08 | Late event and immature-target fixtures keep source/event time, maturity cutoff, affected count, exclusion/unavailable reason, and affected support blocker. |
| Proposal T05 | Duplicate join and unexplained population filter retain count/cardinality discrepancies and block affected support until a new validated transformation revision resolves them. |
| Graph acceptance | Zero denominator, grouping scope, exclusions, missing group meaning, and source-role mismatch run through real computation/check evidence; numeric results originate only in Python. |

## Project Structure

Documentation for this feature:

- specs/007-data-identity-measurement/plan.md
- specs/007-data-identity-measurement/research.md
- specs/007-data-identity-measurement/data-model.md
- specs/007-data-identity-measurement/contracts/data-measurement-v1.md
- specs/007-data-identity-measurement/quickstart.md
- specs/007-data-identity-measurement/tasks.md
- specs/007-data-identity-measurement/execution.json
- specs/007-data-identity-measurement/verification.md (created by the final implementation unit after checks actually run)

Graph-owned implementation paths:

- src/analytics/data.ts
- src/analytics/measurement.ts
- src/analytics/acceptance/007.test.ts
- python/analytics_worker/descriptive.py
- src/analytics/descriptive.ts

**Structure Decision**: Keep source and transformation evidence in data.ts, denominator reconciliation/findings in measurement.ts, numeric work in the authoritative Python module, and the invocation/binding facade in descriptive.ts. Use one feature acceptance file and no additional runtime service, package, migration, or controller/shared-file change. The data-measurement-v1 contract documents 007 data lineage around the already-frozen request/response contracts; it does not replace or extend the normative 001 wire schemas.

## Known Limits

- A version/digest proves identity, not semantic correctness or a source's business authority.
- A query time without repeatable source guarantees cannot support an exact-snapshot claim.
- Workflow grant checks do not claim host-level isolation if the local worker can read files outside the product boundary.
- A passing cardinality or denominator check establishes only its declared keys, counts, and measurement scope; it does not certify every possible analytical assumption.
- A calculation on the mature, eligible subset does not automatically answer a question about immature or excluded observations. The scope and affected count remain visible.

