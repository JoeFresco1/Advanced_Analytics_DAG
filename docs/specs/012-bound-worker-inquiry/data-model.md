# Data Model: Bounded Inquiry

These feature-local types are projections and decision inputs over authoritative feature 008, 009 and 010 records. They do not replace those records or create another execution/history store.

## InquirySnapshotV1

One immutable controller input for an admission or stop decision.

| Field | Meaning and validation |
| --- | --- |
| inquiryId | Stable inquiry reference. |
| questionRef, originalQuestion | Exact question identity and preserved user wording. |
| currentPlanRef, planRevision, planRefs | Locked/current plan plus linked prior revisions from feature 008. Revisions are monotone and bounded by the fixture allowance. |
| priorResultRefs, exposureRefs | Committed earlier evidence and exposure records; absent history is explicit rather than an empty assertion of zero exposure. |
| policyRef, requiredCheckRefs | Frozen policy and exact required checks from feature 010. |
| graphRevision, dependencyRefs | Current admitted graph identity and resolved prerequisite records. |
| budgetRef, reservationState | Remaining enforced counts/time and shared resource reservations. |
| harnessRef, contextDigest | Invocation/harness identity and digest of the exact controller-generated packet. |

## ProposalV1

An untrusted, bounded candidate action authored by the worker.

| Field | Meaning and validation |
| --- | --- |
| proposalId, proposerRef | Stable proposal ID and attributed worker/harness. |
| questionRef, planRef, purpose | The question and locked Plan addressed; purpose must be nonempty. |
| dependencyRefs | Exact admitted task references; all must resolve and the resulting graph must be acyclic. |
| expectedOutputs | One or more typed output declarations with exact binding/role. Missing output declarations fail admission. |
| identity | AttemptIdentityV1 for no-progress assessment; each required identity dimension is explicit or unknown. |
| semanticAssessment | Worker-authored relevance/usefulness reason and cited evidence refs. This is attributed judgment, not mechanical proof. A disputed assessment is unresolved until a named reviewer decides. |
| resourceDemand | Aggregate CPU, worker and source-query demand for parent and nested children, plus memory/source-byte estimates when available. |
| costTelemetry | Per resource: observed, estimated or unavailable; unavailable has no numeric value. A dry estimate does not guarantee spend. |
| replicationPurpose, newObservationRefs, methodChangeReason | Explicit exception grounds for continuing after a proven exact identity. A substantive method change points to a new Plan revision. |
| hardGuaranteeRequired | Declares whether execution depends on a host-enforced resource/cancellation guarantee. Unsupported hard guarantees refuse admission. |

## ProposalAdmissionRecordV1

An append-only run event emitted by the controller before dispatch.

| Field | Meaning and validation |
| --- | --- |
| eventId, inquiryId, proposalId | Stable idempotency and inquiry/proposal references. |
| decision | One frozen result: admitted, rejected, unresolved or budget_exhausted. |
| purpose, dependencyRefs, expectedOutputs | The decision's exact analytical purpose and graph/output contract. |
| proposerRef, semanticReason, evidenceRefs | Attributed proposal interpretation with exact cited refs. |
| identityAssessment, matchingPriorRef | Shared predicate result and prior match reference when one exists. Unknown dimensions stay unresolved. |
| reservationRef, declaredDemand, costTelemetry | Reservation and honest demand/telemetry classification. |
| graphRevision, policyRef, occurredAt | Exact graph and policy context and timestamp. |
| reason, predecessorRef | Bounded decision rationale and related earlier attempt/execution when applicable. |
| durableEventRef | Feature 009 AnalyticsEventEnvelopeV1/committed-manifest reference. admitted is not returned until the corresponding event is committed or idempotently replayed. |

## AttemptIdentityV1

The single exact analytical identity shape shared by 012 and 023.

| Required dimension | Comparison rule |
| --- | --- |
| sourceVersionRefs | Exact source/input version references; unknown prevents exact. |
| sourceRoles | Exact declared source roles; unknown prevents exact. |
| population | Exact scoped population/partition identity. |
| denominator | Exact denominator definition and meaning; known changes are different. |
| target | Exact target/estimand or prediction target. |
| transformations | Ordered transformation identities/parameters. |
| method | Method profile and version. |
| code | Code digest. |
| parameters | Frozen method/search parameters. |
| evaluationContract | Split, metric, and evaluation contract identity. |

compareAnalyticalIdentityV1(left, right) returns per-dimension equal/different/unresolved. Exact requires every required dimension to be known and equal. A known difference cannot be exact; an unknown required dimension is unresolved. Unresolved identity alone does not reject a proposal. Feature 023 imports this function and may not reimplement the predicate.

## AllowanceV1 and ResourceReservationV1

| Field | Meaning and validation |
| --- | --- |
| totalWorkMs / remainingWorkMs | At most 900,000 ms admitted work in the fixture profile. |
| workerInvocations / optionalBranches / planRevisions | Limits 12, 4 and 3 respectively. |
| commandTimeoutMs | 300,000 ms per command. |
| requiredCheckReserve | Sum of the declared/capped requirements of all pending mandatory checks. |
| finalizationReserve | One worker invocation and up to one command timeout for final prose, protected from optional work. |
| resources | Shared reserved/consumed/remaining CPU, worker and source-query demand, aggregated across nested children. Memory/source-byte controls retain observed/enforced/estimated/unavailable status. |
| deadlinePolicy | hard_cancellation only after the host active-process capability test passes; otherwise scheduling_cutoff. |
| telemetry | Value, unit, source and status. Missing token/currency/memory/source-byte telemetry is unavailable, not zero. |

## ExecutionAttemptV1

One execution invocation, distinct from an analytical attempt.

| Field | Meaning and validation |
| --- | --- |
| executionId, attemptRef, planRef | New execution ID; link to the unchanged analytical attempt or newly revised Plan as applicable. |
| failureClass | Classified infrastructure, timeout/cancellation, access, code/worker failure, analytical outcome, or output-contract failure. |
| predecessorRef, reason | Links a retry/repair to the failed execution and states why the controller admitted it. |
| frozenInputs, codeDigest, environmentRef | Exact bindings for the invocation. A classified infrastructure retry retains the same analytical inputs. |
| processStatus, analyticalOutcome | Separate facts. An unfavorable valid result is not a process failure. |
| usage | Measured/enforced/estimated/unavailable per resource and cost dimension. |

## InquiryAnswerProposalV1 and TerminalAssessmentV1

| Field | Meaning and validation |
| --- | --- |
| answerProposal | Worker-authored scoped answer or explicit non-answer, semantic reason and evidence references. |
| attribution | Worker proposer; named reviewer if a semantic dispute was reviewed. |
| evidenceAssessment | Controller verification of required fields, resolvable refs, access, policy and canonical check coverage. |
| terminalOutcome | Exactly one canonical state chosen by precedence from committed evidence: answer, needs_input, budget_exhausted, execution_blocked, insufficient_evidence. |
| fallbackStatus | Deterministic rendering of committed facts and missing interpretation; does not invent a claim and does not require another worker invocation. |
| stopReason, consumed, remaining | Explicit stopping reason and honest allowance accounting. |
