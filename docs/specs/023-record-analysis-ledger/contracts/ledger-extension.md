# Contract: 023 Ledger Extension

This document consumes the version-1 contracts in docs/build/interfaces.md. It does not redefine shared command, hook, event-envelope, state, or project-memory types.

## Provider identity and ownership

The feature-owned module is src/analytics/ledger.ts and exports analyticsExtensionV1: AnalyticsExtensionV1. Its extension ID is ledger and its dependency list contains catalog. The existing 003 loader is the only registry/dispatcher. The provider uses 009 project-memory APIs, 019's access-filtered catalog/search projection, and 012's compareAnalyticalIdentityV1 predicate.

## Command

dag analytics ledger show --inquiry <id>

Resolve the requesting principal through the existing command context. Apply current project/source access before returning any metadata or refs. Return an ordered, readable attempt timeline with question scope, method/parameters, data and transformation versions, execution/evidence status, explicit Result or interpretation absence, next-action rationale, and permitted authoritative refs. Restricted or redacted facts are represented by safe availability reasons without protected values or paths.

## Lifecycle hooks

- orient(context): Search only accessible committed attempts for the inquiry's scoped purpose. Use the 012 identity comparison plus an attributed semantic relevance assessment. Return bounded AnalyticalMatchV1 records and the ledger lookup's retrieval/exposure refs to the next provider. Run before 020 answer-card ranking. Do not rank answer cards or supply previous outcome content to the worker.
- onAdmitted(context, attemptRef): Append an idempotent admission event with the Plan revision, purpose instance, identity, actor, and stable refs.
- afterExecution(context, executionRef): Append the admitted execution and linked Results/checks/claims or explicit absence. An infrastructure retry adds another execution event to the same attempt.
- beforeFinalize(context, terminalIntentRef): Rebuild the current entry from committed run/project refs, append interpretation and rerun/availability facts when present, and use the controller fallback based only on committed facts when worker prose is missing. Commit through 009 before 003 acknowledges completion.

Stable event/commit IDs make repeated hook delivery and recovery idempotent. A same-ID/different-payload collision is rejected by 009; stale CAS is reread and retried only under the shared attributed-conflict rule. Required hook failure remains visible and prevents a completed acknowledgement.

## Match and repeat rules

The 012 comparator is the only field-level AttemptIdentityV1 comparison. Exact requires known-equal identity dimensions and the same scoped question. Related is reserved for a known allowed change in cohort, window, method, or evaluation contract with the operational question preserved. A known target, denominator-meaning, or source-semantic conflict is incompatible. Unknown required dimensions remain unresolved.

Before an exact repeat is admitted, persist reuse or deliberate-rerun rationale. Reuse is eligible only after current access, source availability, evidence state, and bound revisions are checked; failed/null/inconclusive work remains visible but cannot be silently reused as supported evidence. Authorized replication is a linked attempt with a distinct purpose instance and rationale, even when Plan and identity are unchanged. New windows/cohorts are related attempts with a visible change and comparison link.

## Acceptance surfaces

The 023 synthetic acceptance test exercises provider orientation, hooks/finalization, and the ledger show timeline for the locked FR/SC/EC cases and T48-T51. Full checks and evidence mapping are written to specs/023-record-analysis-ledger/verification.md only after implementation acceptance runs.
