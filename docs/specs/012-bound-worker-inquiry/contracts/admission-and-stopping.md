# Admission and Stopping Contract

This feature-local contract implements the frozen facades in docs/build/interfaces.md. It does not amend that shared file or take ownership of the feature 003 controller, feature 009 store/runner or feature 010 policy registry.

## Admission facade

The controller calls admitProposal(inquiryId, proposal) with its trusted current inquiry context. The worker's proposal does not carry authority, grants, trusted budget values, policy versions or host capability claims. Those are resolved from controller services.

The returned decision uses the frozen outcomes admitted, rejected, unresolved or budget_exhausted and includes a bounded reason, reservation reference, identity/match references, and expected graph revision. Semantic reason and evidence remain attributed to the worker or a named reviewer.

Mechanical validation covers schema, question/Plan reference, accessible cited refs, exact dependency resolution, acyclicity, expected output declaration, frozen policy, identity dimensions, reservation arithmetic and current graph revision. It does not mechanically prove that the proposal is relevant or that an answer is useful.

## Persist-before-dispatch ordering

1. Validate the candidate against the immutable inquiry snapshot and compute its provisional decision/reservation.
2. Construct an append-only ProposalAdmissionRecordV1 containing purpose, dependency refs, expected outputs, declared and reserved cost/resources, semantic rationale/provenance, identity comparison, policy and graph revision.
3. Commit that event through feature 009's run-scoped evidence/event mechanism using a stable idempotency identity.
4. Return admitted only after the commit reports committed or replayed. The dispatch caller may launch the task only after it receives this result and the durable reference.
5. If persistence fails or conflicts, dispatch zero tasks. Preserve the persistence failure, reconcile against the committed manifest, and retry the same logical write only with its stable identity.

Rejected, unresolved and budget-exhausted decisions also retain their stop/match reasons in the inquiry history when the run event service is available. A missing commit is never described as a durable admission.

The acceptance fixture injects an event sink and dispatch spy. It asserts the admission event is committed before the spy observes a launch; when the sink fails, launch count remains zero.

## Shared exact identity and no-progress

012 owns AttemptIdentityV1 and compareAnalyticalIdentityV1. The comparator reports each required dimension as equal, different or unresolved. It reports exact only for complete known equality. The 023 ledger imports this same comparator and layers the frozen semantic question rationale and exact, related, incompatible, or unresolved classification around it.

For 012, an exact prior identity with no new observation, justified method change or recorded replication purpose is a no-progress proposal: reject it, retain the matching prior reference and reason, and do not launch. A recorded replication purpose may proceed within allowance. A known changed denominator is not exact. Unknown input version is an unresolved match, not a proven no-progress repeat, and unresolved identity alone does not reject the proposal. If the proposal proceeds to authoritative computation, feature 009 still requires exact input binding; a bounded acquisition/observation step may first resolve that version.

## Reservations and guarantees

Fixture defaults are 900,000 ms work, 12 worker invocations, four optional branches, three Plan revisions and 300,000 ms per command. Reserve pending required-check costs from their declared, per-command-capped estimates and protect one final worker invocation plus up to one command timeout for final prose. Optional proposals may consume only the unreserved remainder. Deterministic partial-status rendering remains available if prose generation cannot run.

Every parent proposal reserves the aggregate CPU, worker and source-query demand of its nested children against the shared remaining pool. No child gets a fresh copy of the parent allowance. If a proposal requires a hard cap and the adapter cannot enforce it, reject it; if it permits a weaker reservation, state that profile explicitly.

Observed, estimated, enforced and unavailable are separate telemetry labels. Token or monetary values cannot be presented as exact without observable/enforceable telemetry. Missing telemetry is not zero. A dry estimate is not a final-spend guarantee.

## Execution and terminal outcome

The neutral runner receives zero generic retries, one attempt, zero review/repair rounds and no automatic harness fallback. A classified, idempotent infrastructure retry is a separate controller admission and Execution under the same frozen analytical inputs and budget. It keeps the failed execution and uses a new execution ID. A code repair creates a linked Execution; a substantive analytical method change creates a linked Plan revision. A negative, null, inconclusive or conflicting result is not an infrastructure failure.

The worker proposes the semantic answer/non-answer, reason and evidence refs. The controller checks required fields and bindings, then applies the canonical terminal precedence: supported answer, useful uncertain answer, needs_input, budget_exhausted, execution_blocked, insufficient_evidence. The first two are answer outcomes, with answered_with_uncertainty selected for useful but unresolved uncertainty. Process exit status never selects the domain outcome. A missing worker answer invokes the deterministic partial-status renderer over committed facts, labels interpretation missing, and makes no new model call.

## Deadline labels

At 15 minutes, stop admitting new work. Label this a scheduling cutoff while active tasks may continue. Only after the host cancellation test succeeds may the controller advertise hard active-process cancellation. Test and expose the 300,000 ms command timeout separately. If a requested hard cancellation/resource guarantee is unavailable, refuse work that depends on it.
