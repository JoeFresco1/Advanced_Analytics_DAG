# Research: Execute and Inspect Evidence

**Scope**: Resolve implementation choices from the accepted 002 contract and the shared 009/010/011 interface freeze. No external research is needed.

## Decisions

### Call the shared production single-stage coordinator

**Decision**: The fixed demo invokes 009 executeStageV1 with the frozen Plan ref, exact synthetic source and transformation refs, program digest, Python/environment bindings, argument vector, timeout, and declared output contract. It commits using 009 publication and recovery facades.

**Rationale**: The frozen interfaces assign executeStageV1 to 009 and explicitly name 002 as its first fixed caller before 003. This gives the demo the same stage semantics used by the later inquiry path.

**Alternatives considered**: A private child-process runner would duplicate scheduling and evidence binding. Calling 003's inquiry controller would violate the graph dependency and make Slice A wait on a later feature.

### Reuse the normative Plan and computation schemas

**Decision**: Store an immutable synthetic PredictivePlanV1 registration payload produced and validated with the Pydantic schema owned by 001. Bind its stable record reference, revision, and digest to every execution. Use the existing mode-specific Python request/result contract; keep the Python implementation limited to the fixed demo fixture.

**Rationale**: interfaces.md makes Pydantic the one Plan and computation schema source and assigns 002 an exact typed Plan while excluding later 003 and 007 implementations. 002 must not create a second TypeScript Plan schema, change generated schemas, or implement general method registration.

**Alternatives considered**: A hand-written DemoPlan type duplicates domain fields and lacks cross-language conformance. Waiting for 008 registration or 013 predictive computation adds an unlisted dependency. The integration can use the 001 schema for a frozen fixture without claiming to implement those later capabilities.

### Keep calculation in Python and stage it with argv

**Decision**: demo_evidence.py calculates only the deterministic local scenario values. TypeScript constructs the exact request and invokes the Python module through executeStageV1 using an executable/argument vector, never shell interpolation. The returned envelope is validated by the existing Pydantic contract and TypeScript-generated schema.

**Rationale**: The architecture assigns numerical calculation to Python and execution to the neutral package through 009. The synthetic fixture is a producer, not a general 007/013 calculator.

**Alternatives considered**: Numeric recomputation in TypeScript creates a second calculator. Embedding paths or data into a shell command weakens the frozen argument-vector boundary.

### Keep check decisions and presentation in their owning facades

**Decision**: Use the frozen project-owner policy and 010 CheckResult/eligibility behavior on the committed result's exact refs. Use 011 report assertions, HTTP routes, viewer and artifact-serving authorization for display. The demo may assemble these calls but must not duplicate validators, eligibility logic, report binding, HTML sanitization, or access checks.

**Rationale**: The feature graph assigns those behaviors to 010 and 011. The milestone only proves their integration and cannot redefine their transitions.

**Alternatives considered**: A demo-only check or terminal-only trace would fail exact subject binding and the browser-viewer acceptance path. A permissive local policy would violate 010 and the accepted checklist.

### Publish valid results before applying check eligibility

**Decision**: Preserve valid computation outputs in the 009 evidence history, including a negative/no-gain result. Record required Check Results and the 010 supported-presentation decision against exact committed refs. A failed or malformed check blocks dependent support but does not erase a valid result or initiate an analytical retry.

**Rationale**: The proposal and state vocabulary separate commit/evidence, check outcome, claim evidence state, and supported presentation. 009 commits complete immutable evidence; 010 decides which dependent Claim may be presented as supported.

**Alternatives considered**: Discarding results when a check fails loses required evidence. Treating check task exit 0 as check pass or retrying a negative result collapses separate facts and violates I02–I04.

### Use explicit synthetic scenario variants and bounded recovery points

**Decision**: Expose deterministic demo variants for pass, overlap failure, null improvement, malformed check, missing computation output, changed artifact, same-revision concurrent commit, pre-commit interruption, lost acknowledgement/replay, and denied/redacted evidence. Use isolated synthetic run-sidecar roots and stable commit IDs in tests.

**Rationale**: These cases directly cover the 002 acceptance matrix and the primary Slice A T cases while leaving production state and dag.run.json untouched.

**Alternatives considered**: A single happy-path fixture cannot establish that failures are visible or recoverable. Random faults make expected identities, output digests, or replay results unstable.

## Questions Resolved

No requirement clarification is needed. Existing dependency nodes supply the runner adapter and schemas (001), locked fixture registry (18-bootstrap), stage/publication/recovery (009), check policy (010), and report/viewer (011). There is no reason to edit an accepted spec, checklist, shared interface, fixture threshold, reference answer, or another feature's code.

