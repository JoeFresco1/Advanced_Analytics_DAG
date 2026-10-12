# Research: Data Identity and Measurement

## Scope and method

This is bounded contract reconciliation, not external technology research. The approved feature spec, proposal revision 2.6, constitution 1.4.1, state and access contracts, frozen build interfaces, and graph row 007 settle the implementation choices. There are no unresolved product decisions and no external research or research subagents are needed.

## Decisions

### Decision 1: Keep the frozen computation protocol

**Decision**: Use the 001 Pydantic DescriptiveRequestV1/DescriptiveResultV1 schemas, generated TypeScript schema validation, shared dispatcher, and computeAnalytics(request, services) facade boundary. The only numeric calculator is Python; 007 must not edit common contracts, generated schemas, package dependencies, or the lock.

**Rationale**: The interface freeze makes Python the normative source for calculation and TypeScript the admission, binding, policy, and presentation boundary. This also preserves the dependency ownership in graph rows 001 and 007.

**Alternatives considered**: A new TypeScript result schema or TypeScript-side aggregate implementation would create a second interpretation and could fabricate values outside the authoritative computation. A new Python runner would duplicate the neutral runner and dispatcher. Both are rejected.

### Decision 2: Use immutable source and transformation evidence

**Decision**: Model an acquisition as a new source/data-version record bound by RecordRefV1 and exact content/source-native identity. Model every transformation as an immutable record of exact input/output versions, code/environment/execution identity, declared and observed grain/cardinality, counts, exclusions, joins, and time scope. Persist through 009's existing evidence boundary.

**Rationale**: The feature and proposal require exact versions, reproducible lineage, and append-only history. The 009 store already owns commit/recovery, so a 007-specific store would split authority.

**Alternatives considered**: A mutable latest pointer, a filename/timestamp identity, schema hash alone, or a second 007 SQLite index cannot distinguish changing data or preserve exact evidence. They are rejected.

### Decision 3: Preserve unknown historical metadata

**Decision**: Use the canonical unknown sentinel for imported data with no prior grain/cardinality, consistency, definition, or version evidence. Before a new supported transformation consumes a legacy unknown, require its plan to declare expected input grain and key cardinality and require observed key/count reconciliation. The earlier record remains unknown.

**Rationale**: Unknown is not zero, none, or pass, and an inference about old history cannot be retroactively written into an immutable record.

**Alternatives considered**: Infer grain from column names or distributions, default to row-level, or overwrite the import after a later worker declaration. Each silently invents a historical fact and is rejected.

### Decision 4: Reconcile denominators instead of enforcing equal row counts

**Decision**: Have Python produce row/entity/group and descriptive counts from actual fixture/input rows. Have TypeScript measurement validation compare those exact observations with pre-execution declarations and follow every denominator definition through transformation refs, exclusion reasons, unmatched keys, joins, and changed grain. Permit declared one-to-many expansion when its entity denominator and output grain reconcile.

**Rationale**: The proposal explicitly allows justified row multiplication and requires denominator traceability. Equality-of-row-count checks would reject valid event/item expansions and fail to detect some hidden exclusions.

**Alternatives considered**: Require all row counts to remain constant, infer denominator by row count, or calculate values a second time in TypeScript. Rejected because these lose semantic grain, erase valid changes, or violate authoritative computation ownership.

### Decision 5: Emit support-blocking measurement findings, not new global claim states

**Decision**: The 007 measurement service emits exact-subject findings with a canonical check outcome, owning check ID/version, observed/expected evidence, and reason. A finding explicitly identifies whether it blocks dependent support. Controller 003 consumes it through the required-check path; frozen feature 010 owns global policy coverage and supported_presentation_eligible.

**Rationale**: Duplicate joins and other invalid measurement evidence must remain visible and block affected support. At the same time, the frozen state vocabulary and interface assign global supported-presentation eligibility to 010, not the calculator.

**Alternatives considered**: Hide findings in logs, treat every issue as a process error, or let 007 assign a global Claim state/authorization outcome. Rejected because these either lose evidence or cross the ownership boundary.

### Decision 6: Store semantics, freshness, maturity, and access evidence separately from schema identity

**Decision**: Bind units/currency, timezone/time boundaries, key and category definitions, group meaning, freshness evidence, event-time and processing-time/watermark facts, label-maturity rule, and the 001 SourceGrant reference into versioned source/transformation/measurement scope. Compare definitions only when their exact known refs match or an explicit transformation aligns them. Check grants through 001; never create permissions in 007.

**Rationale**: Same schema bytes can carry different meanings, late events can change a window, and missing category meaning can invalidate a group claim. Principal and grants are owned centrally and must be enforced before reads.

**Alternatives considered**: Schema fingerprint as semantic identity; guessed group labels; implicit currency or timezone conversion; a 007 grant registry; universal freshness threshold. Rejected because each invents semantics, authority, or an unapproved policy threshold.

### Decision 7: Define zero-denominator results without inventing a value

**Decision**: For proportions and means whose eligible denominator is zero, preserve denominator zero and report the numeric value as unavailable/null with a reason; never emit zero, NaN, or infinity as the statistic. Retain a valid zero count only when the requested count definition itself yields zero and the statistic is defined.

**Rationale**: A zero denominator makes a rate or mean undefined, while the canonical vocabulary requires unknown/unavailable to remain distinct from zero. The task also explicitly requires a zero-denominator fixture.

**Alternatives considered**: Return 0%, 0 mean, infinity, or process failure. These misstate an analytical fact or discard a valid observation.

### Decision 8: Keep all fixtures synthetic and local

**Decision**: Extend only src/analytics/acceptance/007.test.ts with deterministic synthetic records and actual Python computation. Keep answer/reference data outside worker input using the 18-bootstrap evaluator interface, and preserve its lock and existing answers.

**Rationale**: Architecture and graph define synthetic-only evaluation and assign the fixture registry/lock to 18-bootstrap. Feature 007 owns feature-scoped acceptance, not shared evaluator infrastructure.

**Alternatives considered**: Live customer data, editing the shared lock/reference answers, TypeScript-made result payloads, or standalone expected values substituted for execution are rejected.

## Resolved questions

- The source acquisition identity is a RecordRefV1 over a stable content digest or source-native version, never latest or query time alone.
- A source can be validly acquired while replay is unavailable; record availability/replay separately from consistency.
- A new transformation from unknown historical cardinality requires a pre-execution declaration and observed validation.
- A valid row expansion is usable only when declared output grain, expected join cardinality, distinct entity counts, and denominator lineage reconcile.
- The feature does not set global freshness, policy, or authorization thresholds. It compares against a versioned declared expectation and reports unknown when none exists.
- No global status is assigned by 007. Support-blocking findings are passed to the 003/010 consumer without relabeling results or claims.

