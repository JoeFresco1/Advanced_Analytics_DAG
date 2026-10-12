# Data Model: Integrated Delegated Inquiry

Feature 003 composes immutable, versioned records defined by the shared contracts and focused features. It does not introduce a second Plan, Result, Check Result, persistence, or outcome vocabulary.

## Records and responsibilities

| Record or projection | Required fields/links | Producer and consumer |
| --- | --- | --- |
| Question | Stable reference; original wording; operational interpretation; population/time scope; intended use; assumptions; unresolved definitions; principal and source scope. | CLI intake and worker proposal; controller validates and preserves it for answer and trace. |
| Worker invocation | Invocation ID; harness/model/reasoning and version when available; question/plan/context digests; supplied source, policy, prior-answer, exposure, and capability refs; requested outputs; remaining budget; authored output refs. | Worker adapter; controller uses it to attribute semantic proposals and authored artifacts. |
| Plan registration/revision | Frozen PlanRegistrationV1 profile and generated-schema validation; Question ref; predecessor; author; baseline/candidate; population/grain/target/denominator; prediction time/horizon/maturity; metric/unit/direction/delta; partition/tuning/search/uncertainty/stopping design; check policy; intended use; budget. | Worker authors; 008 registers, locks, and assigns plan_interpretation. |
| Exposure | Stable reference; actor/session; material seen; source/partition/version; time; observed or self-reported status; causing invocation or retrieved record. | Worker/controller/retrieval event; 008 classifies plan exposure and restart context. Unknown remains unknown. |
| Stage proposal/admission | Proposal ID; purpose/question addressed; dependencies; expected typed output; input/Plan/program refs; cost/resource declaration; semantic rationale/evidence; graph revision; admission outcome and reservation. | Worker proposes; 012 validates/adopts; 003 admits only between settled stages. |
| Execution and computation | Attempt/execution IDs; exact Plan, source, transformation, program, environment, dependency-lock and seed bindings; timestamps; process outcome; structured result refs. | 009 executes and commits; 007/013 Python calculators produce authoritative numeric payloads. |
| Check Result | Check/execution/Plan/policy/validator refs; exact subjects; canonical check outcome; observed/expected values; evidence refs; tested failure classes and limits; reason. | 010 frozen checks; controller verifies current exact subjects before claim presentation. |
| Result and Claim | Result quantities with units, denominator, interval, scope, source/transform/execution refs; Claim wording, predictive classification/evidence state, supporting/challenging refs and limitations. | 013 computes comparison, worker interprets; controller validates evidence and claim fields. |
| Report assertion | Assertion text; exact Result selector; denominator, unit, population and period; display formatting; support eligibility and reason. | Worker proposes narrative; controller binds it; 011 viewer renders after access check. |
| Analytics extension | schemaVersion 1; extensionId; dependsOn; typed command list; hooks; module/export identity; capability state and digest. | Fixed registry in 003; provider facade owns its analyticsExtensionV1 value. |
| Terminal intent | Inquiry/finalization IDs; canonical outcome and attributed semantic reason; evidence-completeness assessment; committed run revision; stable project commit IDs; required hook acknowledgements; replay state. | 003 controller derives outcome using 012 precedence and commits through 009. |
| Project-memory commit | Stable commit ID; project ID; expected project-manifest revision; principal; records/events and refs to committed run evidence. | 020/023 and later memory providers supply records; 009 commits/replays; 003 coordinates acknowledgement. |
| Run completion acknowledgement | Stable event/commit ID; terminal-intent ref; committed project-manifest ref; acknowledged required hook refs; completion timestamp. | 003 controller appends after all required writes are durable. |

## Relationships and transition rules

1. One Question may have multiple immutable Plan revisions; a substantive revision points to its predecessor and preserves prior exposure.
2. One Plan may produce multiple admitted stages and Executions. An infrastructure retry or code correction is a separately identified Execution under retained history; it does not turn a negative analytical result into a retry request.
3. Each authoritative Result binds to its exact computation, source version, transformation, Plan, code, environment, and output. An opaque worker-only value has no authoritative binding and cannot be a supported report assertion.
4. A Check Result is current only for its exact subject revisions and policy. A changed code or policy revision leaves the old check visible but insufficient for the new execution.
5. Predictive classification is exactly improvement, meaningful_improvement_excluded, or inconclusive under 013's locked favorable-direction gain interval and delta. A failed required validity check leaves the predictive Claim unsupported rather than assigning a predictive classification.
6. Inquiry outcomes use exactly answered, answered_with_uncertainty, insufficient_evidence, needs_input, budget_exhausted, or execution_blocked. Controller policy applies the 012 precedence rule; process/task status cannot select the outcome.
7. Finalization is replayable in this order: run terminal-intent commit; required project-memory commits; run completion acknowledgement. Same stable ID plus same canonical payload replays; changed payload under an old ID is rejected. The controller exposes no completed response before all required acknowledgements.

## Extension registry and lifecycle

The 003 loader validates unique extension IDs and command token paths, exact allowlisted module/export names, required argument schemas, dependencies, and acyclicity. A stable topological order determines hook execution. Only an absent exact optional module is represented as unavailable. Import errors, invalid provider exports, missing dependencies, duplicate commands, cycles, and required hook failures are explicit errors that preserve the incomplete history.

The fixed provider list and hooks are detailed in [the interface contract](contracts/delegated-inquiry-v1.md). Command results expose access-safe display data and committed record refs. Invocation context contains authenticated principal and injected controller services; a provider cannot create privileges or edit controller policy.
