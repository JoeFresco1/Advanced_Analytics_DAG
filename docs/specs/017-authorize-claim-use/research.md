# Research and Engineering Decisions

**Feature**: 017 — Claim use authorization and lightweight Decisions  
**Inputs**: Accepted `spec.md`, proposal revision 2.6 §12, canonical state vocabularies, access and judgment contract, shared architecture/interface freeze, and feature graph.

This is bounded repository research against approved local contracts. No external research or research subagents were used; there are no unresolved product questions.

## Decisions

### Use rules are a separate immutable owner-controlled record

- **Decision**: Define `UseAuthorizationPolicyV1` in 017. It stores policy-owned triggers, scope/audience, authority route, required review types, rationale requirements, and allowed risk acceptance conditions. It references the exact 010 evidence policy revision whose checks and supported-presentation eligibility it consumes.
- **Rationale**: 010 owns frozen evidence/check policy; 017 owns intended-use transitions. A separate 017 record prevents worker control and avoids changing frozen shared/010 schemas while keeping the release rule attributable and versioned.
- **Alternatives considered**: Add use triggers to 010 or a shared interface (changes frozen ownership); hard-code consequential triggers in a Claim mode (mode does not determine stakes); accept worker-proposed triggers (violates policy authority). Rejected.

### Authorization is bound to an exact evidence and scope set

- **Decision**: Pin Claim, supporting/challenging Results, Checks, Reviews, Plan/exposure, 010 evidence policy, 017 use policy, current source/artifact refs, audience, requested use and scope. A change produces an append-only invalidation event and a new linked assessment.
- **Rationale**: Canonical states and proposal I03/I05/I09 require current, exact, attributable evidence. 016 owns the reusable dependency invalidation path for recurring corrections; 017 consumes it and applies the same stale rule to its own use bindings.
- **Alternatives considered**: Reuse authorization by Claim ID alone, use latest policy implicitly, or suppress stale status when a reviewer thinks the change is immaterial. Each loses exact evidence/scope or erases the mandatory staleness event. Rejected.

### Authority, reviewer provenance, and trace presentation are different facts

- **Decision**: Persist actor principal and actor category (`worker`, `separate_agent`, `human`) on every review, plus required policy role match, rationale, unresolved issues and outcome. `trace_seen` is a separate event. Only the configured authority can issue the policy decision; a distinct agent review is not treated as empirical replication.
- **Rationale**: Proposal §12 and the access/judgment contract require attribution and prohibit inference of comprehension or meaning from a mechanical event. This directly tests worker versus distinct reviewer attribution and configured-owner authority.
- **Alternatives considered**: Treat opening the trace as approval, treat a worker's own review as independent, treat any separate model as an authority, or collapse all review into an `approved` boolean. Rejected.

### Canonical state vocabulary governs the transition table

- **Decision**: Use only `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`, `stale`, and `revoked` for authorization. Missing required evidence/review stays non-authorized and pending while its required decision is unresolved. A complete explicit negative authority decision is `denied`. Revocation and material change preserve prior assessments and link a new assessment on reevaluation.
- **Rationale**: This is the normative state-vocabulary contract and accepted FR-007/FR-009; no feature-local synonyms are permitted.
- **Alternatives considered**: Add `blocked`, `expired`, `needs_evidence`, or `approved` as new authorization values. Rejected; explanatory reasons belong in fields and current-validity/event records.

### Separate internal answer completion from consequential action authorization

- **Decision**: `beforeFinalize` leaves a truthful internal answer eligible to complete with uncertainty, including an inconclusive or conflicting finding. It blocks only a policy-declared consequential action or actual external-publication path until a current authorized/risk-accepted assessment exists. Saving an internal report does not itself trigger publication policy.
- **Rationale**: Proposal §12, canonical inquiry outcomes, accepted SC-001/SC-010 and Edge Cases require uncertainty without mandatory interruption and distinguish a saved internal report from external publication.
- **Alternatives considered**: Block the inquiry answer until a human responds or infer publication from report-file creation. Rejected.

### Decisions are immutable pointers, not claims or automated actions

- **Decision**: Store selected action, deciding actor, exact Claim refs, exact current Use Assessment ref, time, constraints/rationale and external action reference. Require complete fields and a current eligible assessment before finalizing. Keep the Decision record inspectable if evidence later becomes stale/revoked.
- **Rationale**: Proposal §5/§12 and FR-006 separate evidence truth from organizational action.
- **Alternatives considered**: Put selected actions on the Claim, mutate the Claim to `approved`, execute external actions from the controller, or store only a free-text note. Rejected.

### Use frozen extension and persistence seams

- **Decision**: Export `analyticsExtensionV1` from `authorization.ts`, with dependency `recurrence`, use-request/review/status and Decision commands plus `beforeFinalize`. Persist through 009 `commitProjectMemory`; use stable IDs and 009 recovery/CAS. Return exact refs using 003's access-safe command result and existing answer/trace route.
- **Rationale**: `docs/build/interfaces.md` explicitly assigns 017's provider and `Use Assessment and Decision` records; 003 owns registry/dispatch and 009 owns all committed writes.
- **Alternatives considered**: Edit 003's registry/controller, write sidecar files directly, add a new store, or alter viewer/runner files. Rejected as ownership and recovery violations.

### Verification uses Node's existing test runner and synthetic/local fixtures

- **Decision**: Use `src/analytics/acceptance/017.test.ts` with `pnpm exec node --import tsx --test`; final feature acceptance also runs `pnpm run build` and `pnpm test`. Include one injected failed project-commit acknowledgement and idempotent replay case.
- **Rationale**: Existing feature quickstarts use this runner path; graph output owns this fixture; the task-generation contract requires runnable unit acceptance. Synthetic data avoids real-source access and reference-answer leakage.
- **Alternatives considered**: Add dependencies, launch the analytics runner, call a real authority service, or execute product checks in planning. Rejected.

## Compatibility and unresolved items

No shared interfaces, package files, accepted spec/checklist, policy thresholds, reference answers, graph, run file, or other feature is modified. No unresolved clarification blocks task generation. The use policy is a 017-owned record referencing the frozen 010 evidence-policy revision; any later shared policy-schema change requires its own accepted contract and migration.