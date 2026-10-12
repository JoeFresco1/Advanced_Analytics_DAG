# Research: Detect Reusable Skill Candidates

This plan has no unresolved engineering unknowns. Per the planning-stage instruction, these decisions were derived from the accepted proposal, feature specification, shared architecture/interface freeze, and feature graph without dispatching research agents.

## Decisions

### Extension and checkpoint integration

- **Decision**: Export the graph-owned `analyticsExtensionV1` from `skill-capture.ts` with extension ID `skill-capture` and frozen dependencies `answers` and `source-reuse`. Use 003's `afterEvidenceCommit` for committed result signals and dependency-ordered `beforeFinalize` for feedback. Post-completion feedback is carried in the terminal-intent/user-request ref for the analyst's next ordinary delegated question; that existing request is the production producer. The `beforeFinalize` hook runs after 020 has committed the follow-on answer card, reads the request and a bounded set of completed inquiry/answer records, and matches feedback to its prior target through an explicit answer ref or the exact card ref in 020's retrieval/exposure event. It suppresses method capture if the target is ambiguous. It reads committed 014 source candidate packages through the 015 `source-reuse` facade. These existing records and hooks provide the producer and dispatch path; there is no new feedback command or callback.
- **Rationale**: `docs/build/interfaces.md` makes 003 the sole registry/controller owner and lists the exact provider dependencies and lifecycle hooks. The 020 finalizer provides durable answers before 021 runs. FR-001 requires automatic checkpoints with no special command; the existing user-request record, hook dispatch, and answer/source facades supply that path without editing the shared controller.
- **Alternatives considered**: Add a skill command, create a second callback registry, or patch 003 controller files. These would contradict the frozen interface and make capture opt-in or give feature 021 shared-file ownership.

### Evidence and durable storage

- **Decision**: Read only committed answer, source-reuse, feedback, validation, and exposure references. Publish candidate/suppression records using 009 `commitProjectMemory` and its replay/CAS semantics. Treat SQLite as a rebuildable index projection.
- **Rationale**: The architecture assigns project memory as durable authority and 009 as the shared CAS/recovery primitive. Answer cards and candidate skills have separate meanings and records.
- **Alternatives considered**: Write a local catalog table, modify answer-card storage, or implement a capture-specific log/CAS. These duplicate ownership or erase the result-memory/guidance boundary.

### Capture trigger classification

- **Decision**: Use typed positive signal variants for explicit method/rule endorsement, repeated validated work across independent inquiries, costly discovery crossing its declared query/time threshold, and a validated correction reused later. Route answer-only praise to answer annotation and record a suppression reason for reusable guidance.
- **Rationale**: This covers FR-002 and the four positive cohorts frozen in SC-009. Proposal §21 also recognizes answer approval and durable recipes but prohibits treating enthusiasm or a single result as general authority.
- **Alternatives considered**: A sentiment/model-confidence score, significance, file count, or untyped free-text classifier as eligibility. These do not meet the evidence and authority rules in FR-006.

### Identity, revision, and scope

- **Decision**: Deduplicate by typed procedure identity and compatible exact scope; persist a suppression/link decision for repeated identical signals. A changed procedure creates a linked candidate revision. Scope cannot exceed supporting sources, roles, periods, audience grants, or validated examples.
- **Rationale**: FR-005 and SC-007 require visible deduplication/revision behavior. Constitution IV and access contract require precise scope and prevent memory from granting privileges.
- **Alternatives considered**: Similarity of praise text alone, overwriting the existing candidate, or merging across unknown scope. These conflate separate procedures or erase analytical history.

### Authority conflict and exposure

- **Decision**: Record exact owner-curated definition and owner refs on a conflict; leave the candidate pending for 022. Keep holdout/prior-result refs outside generic guidance and link their exposure provenance. If the boundary cannot be maintained, preserve the answer card and suppress method/rule capture.
- **Rationale**: FR-008 and canonical candidate lifecycle reserve activation/authority transitions for 022. Invariants I05, I16, and I17 preserve prior exposure and separate result memory from reusable instructions.
- **Alternatives considered**: Resolve the owner conflict in 021, drop outcome provenance, or copy a study result into general instructions. Each violates the feature boundary or accepted invariants.

### Checkpoint budget and evaluator

- **Decision**: Bound checkpoint work with the immutable capture policy and remaining time/work reservation supplied by the controller; do not invent default numeric limits. Consume the frozen 48-signal fixture and evaluator API from 18-bootstrap as read-only. Pin/select the fixture batch, obtain evaluator-only references only after capture responses complete, and call `adjudicateResponseV1` through `src/analytics/evaluation/registry.ts`. Apply the registered precision/recall definitions and report proposal IDs and numerators/denominators; let missing labels or denominators fail closed.
- **Rationale**: FR-007/SC-004 require fixed bounds while the accepted contract does not set a candidate-specific constant. 18-bootstrap owns the immutable fixture/evaluator API and metric registry; 018 owns downstream whole-product release aggregation. Thresholds are frozen before evaluation.
- **Alternatives considered**: Add arbitrary constants during feature planning, alter fixture labels after results, tune thresholds, or make capture an unbounded extra worker invocation. These would weaken the approved policy or steal finalization budget.

## Resolved Questions

- Which registry? Feature 003's fixed `AnalyticsExtensionV1` registry.
- Which persistence? Feature 009 `commitProjectMemory` / `recoverProjectMemory`.
- Which candidate consumer? Feature 022; 021 never activates.
- Which evaluator API scores the 48 capture signals? 18-bootstrap `src/analytics/evaluation/registry.ts`, using the frozen capture cohort and registered metric definitions. Feature 018's whole-product release assessment is not a 021 runtime dependency.
- Which user-supplied data? Synthetic/local fixtures only.
