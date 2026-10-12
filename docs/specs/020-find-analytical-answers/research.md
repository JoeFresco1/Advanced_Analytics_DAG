# Research and Design Decisions

## Context

The accepted proposal, 020 feature spec, state vocabulary, access/judgment contract, and frozen build interfaces already settle the relevant storage, ranking, access, and hook boundaries. The feature graph assigns only `answers.ts`, `retrieval.ts`, and `acceptance/020.test.ts` to 020. This is a bounded local design pass; no external research or clarification is needed.

## Decisions

### Durable answers and deterministic fallback

**Decision**: Build a versioned answer card during the existing 003 finalization hook. `answers.ts` reads committed inquiry facts and writes the card through 009 project-memory commit/recovery. If worker interpretation is absent, write a scoped fallback that states interpretation is missing or the inquiry is incomplete and links committed facts without inventing an answer.

**Rationale**: FR-001/009 and SC-006/015 require durable finalization and a worker-without-summary path. 009 owns idempotent CAS, recovery, and project-memory commit; 020 owns the record content. Reusing the handoff prevents duplicate stores and makes a lost acknowledgement replayable.

**Alternatives considered**: Write a card directly from the worker summary; rejected because the worker may omit prose and cannot author committed records. Use the catalog as the only record; rejected because 019's SQLite index is a projection. Add a new 020 persistence layer; rejected because it duplicates 009 ownership.

### One prior-attempt match call

**Decision**: Treat the `AnalyticalMatchV1` result from 023's earlier `orient` hook as immutable input to 020 orientation. 020 never invokes the ledger matcher. Verify one 023 match call, then one 020 retrieval event in the integration fixture.

**Rationale**: `docs/build/interfaces.md` freezes dependency-ordered hooks with the 023 assessment before 020 ranking, and `docs/build/ARCHITECTURE.md` says 020 calls the ledger match once during orientation. The earlier planning rejection identified that 023 already supplies that assessment.

**Alternatives considered**: Call 023 again from `retrieval.ts`; rejected because it duplicates work and can create a second rationale/exposure event. Let 020 reimplement analytical identity; rejected because 012/023 own that comparison and canonical match decision.

### Access-first deterministic ranking

**Decision**: Ask 019 for the current principal's accessible candidate set before calculating any ranking or rendering any title. Apply exact structured scope compatibility first, then a fixed lexical/structured relevance order with stable tie-breaks. Unknown required scope is never direct. Pins and recency can only resolve otherwise equal cards and are disabled in the frozen ranking score.

**Rationale**: FR-014/015 and SC-004/009/010/012 require scope-safe classification, deterministic known-field checks, an explicit no-match, and ranking measured without pins. The 150/20 relevance labels remain independent evaluator data.

**Alternatives considered**: Rank every catalog row before filtering; rejected because restricted titles/IDs could influence context and results. Use pins or recency as score boosts; rejected because they could override relevance and invalidate the pinless benchmark. Introduce vector search now; rejected because the frozen local path specifies lexical/structured retrieval and does not establish a need or measured privacy/cost profile.

### Separate answer curation from analysis meaning

**Decision**: Store pins, annotations, and supersession as append-only curation events linked to stable card IDs. An explicit “remember this analysis” finalization intent creates a pin event for the scoped answer card. Never change Claim evidence state, answer scope, or current validity from a curation event. Rebuild the projection from committed records and events.

**Rationale**: FR-004/016 and SC-003/014 distinguish importance from evidential meaning and require rebuild survival. T40 requires a pin-on-finalization path that does not create a skill.

**Alternatives considered**: Store pin state only in SQLite; rejected because index rebuild would lose it. Treat a pin as evidence approval; rejected by the access/judgment contract and FR-004. Convert “remember” into a skill; rejected by the feature spec and T40.

### Record retrieval exposure and later use separately

**Decision**: Commit a `RetrievalEventV1` before supplying prior answer material to a worker. Append worker use/rejection rationale as a linked disposition after the worker returns. Keep evaluation exposure in the controller exposure ledger; include the retrieval event/ref in that record.

**Rationale**: FR-006/011/017 and SC-007/012/014 require provenance, one retrieval event, attributed worker disposition, and exposure retention. A later response cannot be known before context is supplied, so the use reason is a subsequent append-only event.

**Alternatives considered**: Treat loading as proof the worker used an answer; rejected because the interface records supplied refs, not model comprehension. Wait to record exposure until after the worker returns; rejected because it would omit the actual context exposure if the worker crashes.

### Keep story selection auditable

**Decision**: Preserve the worker's concise scoped story and selection reason with the chosen Claim/Result refs; link non-selected results as supporting or exploratory artifacts. Fallback stories only describe canonical committed fields and do not perform semantic selection.

**Rationale**: FR-008 and SC-005 require a readable short answer while preventing editorial ordering from becoming a change to evidence support. Multiple competing Claims remain linked rather than being collapsed into one conclusion.

**Alternatives considered**: Automatically choose the numerically strongest Claim; rejected because materiality and relevance are not proof of support and the claim can be unsupported or conflicting. Promote all inventory artifacts to answers; rejected by FR-003 and the 99-file acceptance case.

## Resolved Questions

- Storage and commit owner: existing 009 project memory.
- Search and access filter: existing 019 SQLite/FTS5 catalog adapter.
- Prior attempt match owner and call count: 023 once before 020 ranking.
- Extension export and hook order: frozen 003 `AnalyticsExtensionV1` seam.
- Viewer owner: 011 existing answer/trace routes consume stable card and ledger refs.
- Acceptance fixture: synthetic/local only; 018 evaluator labels remain outside worker context.
- Ranking target, no-match count, and 99-file goal: retained exactly as specified.

No `NEEDS CLARIFICATION` items remain.
