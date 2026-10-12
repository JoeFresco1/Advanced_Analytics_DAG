# Data Model: Analysis-Attempt Ledger

The ledger is a readable projection over committed domain records and append-only project event envelopes. It is not a competing result store. Exact field representations continue to use the frozen interfaces and canonical state vocabulary.

## LedgerAttemptV1

One admitted comparison purpose under a specific Plan revision.

- Stable attempt record reference, project/inquiry/question refs, Plan ref and predecessor/related-attempt refs.
- Purpose-instance identifier. A distinct authorized replication receives a new purpose instance and linked attempt even if the Plan and identity do not change.
- AttemptIdentityV1 reference/value with exact source-version and source-role refs, population, denominator definition, target/estimand, ordered transformations, method/profile, code digest, canonical parameter digest, and evaluation-contract digest.
- Operational question scope, period, population and source-role summaries, subject to current access filtering.
- Ordered immutable event refs and linked execution, candidate-model, Result, Check Result, Claim, evidence, and exposure refs.
- Current readable projection of controller-derived execution/evidence/availability facts, separated from worker-authored interpretation and its actor/reason/evidence provenance.
- Explicit absence markers when a Result or worker interpretation does not exist. Negative and inconclusive evidence remains evidence; neither is promoted to a supported answer.
- Current reuse eligibility computed against current grants and source/artifact availability. Historical evidence refs remain stable when eligibility changes.

## Ledger event envelope and payload

Each domain event is wrapped in AnalyticsEventEnvelopeV1 from the shared store contract: schema version, stream ID, monotone sequence, stable event ID, kind, actor ref, occurrence time, payload record ref/digest, and prior-event digest. A committed event's bytes and ID never change.

The ledger-owned payload kinds represent admission, execution/evidence linkage, worker interpretation or explicit absence, reuse/rerun rationale, and availability/exposure reassessment. An event carries the stable attempt ref and only the refs and bounded display facts needed to rebuild the projection. A new fact appends a new event; correction, interpretation, or rationale never overwrites an earlier payload.

## AnalyticalMatchV1

A prior-attempt assessment contains schema version, prior attempt ref, one canonical status (exact, related, incompatible, or unresolved), per-dimension comparison outcomes, attributed question-relevance proposer/reason/evidence refs, and a concise match rationale.

- Exact requires every required identity dimension to be known and equal and the scoped operational question to match.
- Related requires the same scoped question plus a known allowed change such as cohort, window, method, or evaluation contract.
- Incompatible identifies a known target, denominator-meaning, or source-semantic conflict.
- Unresolved identifies each unknown required dimension; wording similarity cannot make it exact.

The shared 012 comparator supplies dimension comparison; the ledger adds semantic question relevance and reason provenance.

## Rerun rationale and exposure

Before an exact repeat proceeds, record whether valid prior evidence is reused or the repeat is deliberately authorized, with actor, rationale, evidence refs, and linked prior attempt. Intentional replication creates another attempt; an infrastructure retry does not.

Every ledger lookup returned in orientation is access-filtered and tied to the requesting inquiry through an exposure/retrieval reference. 023 returns bounded match assessments to 020, not prior answer content. If 020 includes an outcome in worker context, that actual outcome exposure is recorded by the 020 retrieval path against the same inquiry and source refs.

## State and transition rules

- Do not add ledger-specific synonyms for canonical inquiry, check, claim, availability, or match states.
- An execution may fail while its Result is absent; the projection records both facts separately.
- Null, inconclusive, failed, stopped, superseded, and incomplete attempts remain queryable with their original refs/revisions.
- A source redaction or grant revocation appends/rebuilds current availability and reuse eligibility without removing access-safe historical entries.
- Project recovery trusts the committed manifest chain, replays stable commit IDs idempotently, and does not promote orphan event files.
