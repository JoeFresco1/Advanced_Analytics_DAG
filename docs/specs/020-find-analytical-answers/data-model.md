# Data Model: Answer Cards and Retrieval

This model refines the frozen version-1 types in `docs/build/interfaces.md`. It adds no canonical state values, storage engine, or commit protocol. Records are immutable and use stable IDs, revisions, provenance, and digests; current views are rebuildable projections over committed records.

## AnswerCardV1

**Owner**: `src/analytics/answers.ts`  
**Producer**: 020 `beforeFinalize` hook, called by 003 with a terminal-intent reference  
**Durable consumer**: 009 `commitProjectMemory`  
**Viewer**: 011 answer/trace path

Fields follow the frozen interface:

- stable `cardId`, `revision`, and inquiry reference;
- original question and a concise scoped answer/non-answer;
- key Claim/Result references and the worker's selected-finding reasons;
- population and period;
- canonical inquiry, Claim/evidence, and current-validity values, with uncertainty and limitations;
- committed artifact references and durable curation references;
- a work-ledger timeline link derived from the stable `inquiryRef` through the existing `dag analytics ledger show --inquiry <id>` path; this adds no `AnswerCardV1` or shared-interface field;
- explicit missing-interpretation or incomplete-work marker when fallback is used.

The story is a compact editorial view of the inquiry, not a new evidence state. Non-selected outputs remain linked in their supporting/exploratory roles. A deterministic fallback reads only committed records. It does not manufacture a direction, effect, significance, recommendation, or semantic interpretation.

## CurationEventV1

**Owner**: `src/analytics/answers.ts`  
**Durability**: Append-only project-memory event through 009

Each pin, title/annotation, correction, or supersession decision has a stable event ID, card reference/revision, event kind, actor/provenance, reason or annotation, creation time, and links to affected/current card revisions when applicable. Current pin and annotation views are derived from the event history. An explicit “remember this analysis” intent at inquiry close creates a pin event for the finalized scoped answer. The event cannot change Claim evidence state, answer scope, access scope, current validity, or authorization.

## RetrievalEventV1

**Owner**: `src/analytics/retrieval.ts`  
**Candidate source**: 019 access-filtered index  
**Durability**: Append-only project-memory event through 009

Fields follow the frozen interface:

- requesting inquiry and original query;
- structured source, metric, period, Claim-state, population, and pin filters;
- candidate card IDs only after current access filtering;
- ranked cards and each card's `direct_answer`, `related`, or `not_relevant` class;
- exact match reason, visible scope mismatch, current evidence/validity version, and supplied references;
- exposure reference and stable event ID.

The query outcome is exactly `direct_answer_found` or `no_match`. A later `RetrievalDispositionV1` event links the event ID to the worker's attributed use/rejection rationale and evidence refs. This append-only follow-up does not rewrite what was exposed.

## Prior-attempt handoff

023 owns `AnalyticalMatchV1` and calls the shared identity predicate. During `orient`, 003 runs ledger orientation before answer orientation. 020 consumes the already-returned assessment as a read-only handoff, preserving its status, compared dimensions, proposer, reason, and evidence refs. 020 does not call 023's match service again and does not reinterpret an incompatible known dimension as an answer-card direct match.

## Project-home projection

`retrieval.ts` derives an access-filtered home list from committed cards and curation events. It groups recent, pinned, and unresolved cards while preserving stable card, evidence, and ledger references. Negative and unresolved answers remain eligible. The 011 answer/viewer path opens the stable answer route; no query is required to construct the home list. It is a projection, not a new record owner.

## State and transition rules

- **Finalization**: terminal inquiry intent → committed answer card (or deterministic fallback) → completion acknowledgement. Replayed hook delivery uses the same stable commit/event IDs. Failure to durably record the card prevents a completed acknowledgement.
- **Curation**: each user action appends an event. An index rebuild replays events to derive pin/annotation state without losing history.
- **Current validity**: source, artifact, scope, or meaning changes create a new current assessment or supersession link. Historical cards remain available subject to current access and availability checks.
- **Retrieval**: access-filtered candidates → per-card classification → deterministic ranking → durable exposure event → bounded worker context → appended worker use/rejection disposition.
- **Compatibility**: only known compatible question intent, metric, population, period, source meaning, and current evidence can yield `direct_answer`. A known mismatch can be `related` with its reason; missing compatibility is never direct. A card with no substantive question connection is `not_relevant`.
- **Tie-breaks**: pin and recency are considered only after all relevance, validity, scope, and access criteria are equal. They are disabled in the frozen ranking evaluation.

## Access and failure boundaries

Search, project home, and card metadata re-check the current principal and source grants. 019 filters before ranking; 011 checks access again when a trace or artifact is opened. Redacted/unavailable evidence retains safe historical metadata and a visible availability reason; protected titles, paths, and values are not returned. The local worker's broad shell access is reported as workflow-level when that is the actual boundary.

No-match remains valid for an empty corpus or no qualifying direct answer. Negative, inconclusive, stopped, and failed work remains findable with its canonical state and is not labeled a failed process solely because its analytical outcome is unfavorable.
