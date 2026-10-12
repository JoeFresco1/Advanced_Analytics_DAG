# Research: Bounded Worker Admission and Stopping

The accepted feature spec, proposal revision 2.6, canonical state vocabulary, access/judgment contract, architecture, and frozen interfaces resolve the implementation choices below. There are no remaining NEEDS CLARIFICATION items.

## Decisions

### TypeScript controller facade and existing execution adapter

**Decision**: Implement inquiry policy in the assigned TypeScript modules. Consume feature 008 Plan/exposure, feature 009 stage and persistence APIs, feature 010 policy/check results, and the neutral executor through its existing adapter. Do not create a second scheduler, runner, retry system or store.

**Rationale**: The architecture assigns proposal admission and identity to feature 012 while leaving process launch and durable commit/recovery to their existing owners.

**Alternatives considered**: Adding a feature-local runner or a second persistence log was rejected because it would split authoritative execution/history and violate the frozen ownership boundaries.

### One analytical identity predicate owned by 012

**Decision**: Define AttemptIdentityV1 and compareAnalyticalIdentityV1 once in src/analytics/analytical-identity.ts. Compare every required dimension from ledger FR-006. Return exact only when every dimension is known and equal; report known differences and unresolved dimensions distinctly. Feature 023 imports this predicate and adds its own semantic rationale and canonical ledger classification.

**Rationale**: The architecture/interface freeze explicitly makes 012 the owner and prohibits a second comparison implementation in 023. Unknown identity dimensions cannot prove equality.

**Alternatives considered**: Text similarity, analysis names, partial equality and a duplicate ledger comparator were rejected because none establishes exact analytical identity.

### Durable admission before dispatch

**Decision**: Build a typed proposal decision and append its admission event with feature 009's run-scoped evidence commit service before returning admitted or allowing dispatch. Use stable event/commit identity so a replay is idempotent. A failed or conflicted write means no dispatch. Persist the reason, purpose, dependencies, outputs, cost/resource declaration, reservations, identity assessment, proposer references and graph revision.

**Rationale**: The proposal contract requires a readable record and the controller's decision before work is launched. The shared event envelope and commit path are already frozen by feature 009.

**Alternatives considered**: Returning an in-memory admission and recording it after execution was rejected because a crash would leave launched work with no admission history. A new 012-specific event store was rejected because 009 owns atomic publication/recovery.

### Separate semantic judgment from deterministic admission

**Decision**: Require worker reasons and cited evidence for semantic relevance/usefulness. Mechanically validate their presence, reference accessibility, schema, graph shape, identity, budget and policy. Keep a disputed semantic assessment unresolved unless a named reviewer supplies an attributed decision.

**Rationale**: The access/judgment contract bars the controller from presenting a semantic interpretation as mechanically proven.

**Alternatives considered**: Treating a relevance score as proof or silently accepting/rejecting disputed meaning was rejected.

### Reserve required work before optional work

**Decision**: Use the canonical fixture limits: 900,000 ms total admitted work; 12 worker invocations; four optional branches; three Plan revisions; 300,000 ms per command. Before optional exploration, reserve the declared capped cost of each required check, one final-reporting worker invocation and up to one command timeout for final prose. The deterministic status renderer is the no-extra-invocation fallback. If required reservations cannot fit, admit no optional work and report the unmet capacity honestly.

**Rationale**: The proposal and SC-008 require validation/reporting reserve but do not set a numerical reserve smaller than each declared requirement. This rule derives reserve from the actual frozen checks and protects one final response slot without inventing exact check costs.

**Alternatives considered**: A fixed percentage was rejected because required checks differ. Reserving a fixed dollar/token value was rejected because the local harness may not expose or enforce it.

### Aggregate nested resource reservations and refusal

**Decision**: Sum CPU, worker and source-query demand across all child tasks against the remaining shared reservation. Distinguish observed/enforced limits from estimates. If a proposal requires a hard cap and its adapter cannot enforce that cap, return a refusal; if it accepts a weaker local reservation, label the weaker guarantee.

**Rationale**: SC-009 and FR-011 require aggregate—not per-child—accounting and refusal for unavailable required hard limits.

**Alternatives considered**: Giving every nested child the parent allowance or labeling an estimate enforced was rejected.

### Classified infrastructure attempts and outcome precedence

**Decision**: Configure generic execution retries, review/repair rounds and fallback to zero. A classified transient infrastructure retry is separately admitted under the same frozen inputs and analytical attempt, with a new Execution ID and recorded failure. A code repair creates a linked Execution; a substantive method change creates a linked Plan revision. Negative/inconclusive analytical findings never trigger retries. Resolve terminal outcomes using the canonical evidence precedence and retain worker answer/reason attribution.

**Rationale**: Execution success, analytical result and inquiry completion are separate facts. Canonical state values and order are normative.

**Alternatives considered**: Reusing process exit status as an inquiry state, hiding failed attempts, or automatically retrying an unfavorable value was rejected.

### Scheduling cutoff, cancellation and unavailable telemetry

**Decision**: Stop launching at the 15-minute scheduling cutoff. Advertise hard active-work cancellation only after the host cancellation capability test succeeds. Enforce/test the five-minute per-command timeout through the execution adapter. Render token, currency, memory and source-byte limits as estimated or unavailable unless the adapter provides an enforceable measurement; never render unknown as zero or an exact monetary cap.

**Rationale**: The architecture explicitly distinguishes scheduling from active termination and describes local cost/resource telemetry limits.

**Alternatives considered**: Claiming that a scheduler deadline kills current work or treating missing telemetry as free/zero was rejected.

