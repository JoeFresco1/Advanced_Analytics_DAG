# Data Model: Recurring Window Records

All refs are the frozen 009 `RecordRefV1`/`EvidenceRef` shape: stable ID, positive revision, and content digest. Mutable paths do not identify evidence. Committed record bodies are immutable; changes create linked revisions/events. The machine states below reuse the canonical analytics vocabulary.

## Window identity and version

### `WindowIdentityV1`

- `schemaVersion: 1`
- `windowKey`: stable identity of the logical bounded window, derived from source identity and canonical boundary semantics; distinct from any source path.
- `sourceId` and exact source identity/definition refs.
- `eventTimeBounds` and `processingTimeBounds`: start/end timestamps with timezone and inclusivity semantics, retained separately.
- `sourcePosition`: source-native start/end offsets, cursor or equivalent; unavailable facts use explicit `unknown`.
- `completeness`: `provisional`, `finalized`, or `unknown`, with observed-through/expected-through refs or values and reason.
- `lateness`: watermark, allowed-lateness rule, latest arrival/observed lateness, and late-event refs.
- `labelMaturity`: maturity state/value and definition ref where applicable; otherwise explicit not-applicable reason.
- `definitionRef`, population/cohort ref, weighting ref, freshness/as-of ref, and observation grain refs needed for comparison.
- Canonical identity digest computed from all required identity dimensions. Missing required dimensions cannot produce an exact comparison.

### `WindowVersionV1`

- Stable `windowVersionId`, `windowKey`, monotonically increasing version sequence.
- Exact `sourceRef`, `executionRef`, and `identityRef`.
- `predecessorWindowRef` for the prior acquired version; optional `correctsWindowRef` for a late-data correction to a prior logical window.
- Acquisition time, immutable source-position range, correction-event refs, and commit ref from 009.
- Every recurrence has new source/execution refs. Versions 1, 2, and 3 remain readable after two corrections.

## Period comparison

### `PeriodComparisonV1`

- Stable comparison ID/ref and `schemaVersion: 1`.
- Exact `leftWindowRef`/`rightWindowRef` and exact definition refs.
- `state`: one of `unassessed`, `comparable`, `qualified_comparison`, `incomparable`.
- Dimension assessments for definition, population, coverage, weighting, and freshness; each records `match`, `different`, or `unknown`, its source refs, and rationale.
- Ordered `reasons[]` with a stable local reason code, dimension, detail, proposer/attribution where semantic, and evidence refs.
- Optional predeclared adjustment ref/description and bounded `interpretationLimit`; required whenever state is `qualified_comparison`.
- Assessment actor/type and creation ref. This is an assessment of scoped evidence, not a Claim or authorization.

Unknown required metadata yields `unassessed`; a known incompatible value yields `incomparable`; complete matches yield `comparable`; only a documented difference handled by a predeclared bounded adjustment yields `qualified_comparison`.

## Repeated inspection

### `InspectionPolicyV1`

- Locked plan ref, policy ref/version, `plannedLookTimes`, comparison family, procedure (`sequential` or `multiplicity_controlled`), method parameters, and stopping rule.
- Lock time must precede the first relevant look. Missing prior exposure remains unknown.

### `InspectionLookV1`

- Stable look/event ref, policy ref, sequence, execution/result refs, planned/scheduled indicator, looked-at outcome refs, actor/session, time, and exposure evidence.
- Declared looks are evaluated under the locked procedure and retained one by one.
- An undeclared additional look records exposure and yields `outcome_informed` interpretation; it may be described exploratorily but cannot be represented as an independent confirmatory result.

## Correction and dependency impact

### `CorrectionRelationshipV1`

- Stable correction ref, old and new `WindowVersionV1` refs, event-time window key, late event/source-position ref, processing-time arrival, and reason.
- A second correction points to the immediate predecessor and the original logical window; it does not replace either earlier relationship.

### `BoundDependencyV1` and `DependencyImpactV1`

- Directed bound edge from a committed `RecordRefV1` to the exact upstream `RecordRefV1`, with binding purpose and digest.
- A correction impact records root old/new refs, change reason, traversal ID, every discovered path, every affected exact ref, its affected record type, old and new/current-validity projection refs, and commit ref.
- Supported affected types are exactly Results, Claims, Check Results, reports, answer cards, ledger entries, source-skill versions, Use Assessments, and Decision references.
- On discovery, the dependent current validity is `reassessment_required` before interpretation/review. `current` or `superseded` does not override that required transition. Reassessment produces a linked record. Historical result, claim, report, card, ledger, skill, assessment, or decision meaning is unchanged.
- For a source-skill version, this is the current-validity overlay and refresh trigger; 015's compatibility predicate owns whether the immutable skill version is `stale` for later use. 016 does not change the lifecycle state.
- Traversal uses visited record refs to terminate cycles and emits a complete deduplicated reachable set. It recomputes from the committed graph for every correction and retains prior impact refs.

## Persistence and viewer projection

- The run sidecar holds source/window/execution/correction/comparison/inspection records and immutable evidence refs.
- Project memory holds cross-run impact/current-validity events and references to committed run records using 009 CAS/commit primitives.
- Answer/trace projections resolve historical refs and correction/impact refs by ID and show current validity, availability, and reproduction independently. They never resolve authority from a worker path or an uncommitted file.
- The `recurrence` extension returns bounded orientation context and committed ref lists; it cannot change access, policy, support state, inquiry outcome, or authorization.
