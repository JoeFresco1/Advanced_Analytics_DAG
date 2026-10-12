# Feature Specification: Learn and Reuse a Data Source

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Proposal Slice C: learn the structure and meaning of a frequently used data source, publish versioned source guidance, and demonstrate reuse by a later inquiry.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Learn an unfamiliar source (Priority: P1)

An analyst authorizes access to a data source. The worker examines the
available tables, keys, grain, joins, definitions, and time behavior and
creates a compact, reusable source skill with references and tested query examples.

**Why this priority**: Repeated source discovery wastes time and invites
inconsistent metric definitions.

**Independent Test**: Connect a bounded read-only source fixture, generate a
candidate guide, and verify its factual assertions and example queries against
the fixture.

**Acceptance Scenarios**:

1. **Given** a new source, **when** discovery completes, **then** the guide
   identifies when it applies, available structures, grain, key joins, time
   boundaries, freshness, known pitfalls, and runnable read recipes.
2. **Given** a fact inferred from a sample rather than declared by an owner,
   **when** the guide presents it, **then** the source, sample/window, and
   confidence of that observation are visible.
3. **Given** an unknown business definition, **when** the worker publishes a
   candidate, **then** the unknown remains explicit rather than being turned
   into an authoritative rule.

### User Story 2 - Reuse a compatible guide (Priority: P1)

A later worker answers another question about the same source using the
previously learned guide, while checking that access and meaning still match.

**Why this priority**: Writing a guide without later consumption does not
deliver the promised source memory.

**Independent Test**: Run a second independent question and inspect the exact
guide version supplied, references read, and recipes executed.

**Acceptance Scenarios**:

1. **Given** a compatible source and role, **when** a new inquiry begins,
   **then** the worker receives the applicable guide and can use a tested
   recipe without repeating full discovery.
2. **Given** changed units, keys, category meaning, access role, or freshness,
   **when** reuse is attempted, **then** affected guidance is marked `stale`
   and excluded from active instructions. If a validated refreshed version
   exists, the worker uses that version; otherwise the answer names the
   unresolved source meaning and does not claim a supported definition.

### User Story 3 - Keep source memory safe (Priority: P2)

An analyst can reuse guidance without exposing credentials, sensitive rows,
or prior evaluation outcomes as generic source facts.

**Why this priority**: A guide is persistent worker context and can carry
both sensitive data and outcome exposure into later studies.

**Independent Test**: Seed a schema comment with instructions, inject a prior
holdout result into a candidate summary, and inspect the activated package and
new inquiry's exposure record.

**Acceptance Scenarios**:

1. **Given** instructions embedded in source metadata, **when** discovery
   reads them, **then** they remain untrusted content and do not change policy,
   privileges, or the guide's governing instructions.
2. **Given** a study-specific evaluation finding, **when** it is retained,
   **then** it is separated from general source guidance and loading it into
   a later inquiry is recorded as exposure.

### Edge Cases

- Two environments have identical schemas but different metric meanings.
- A source adds an unused column without changing the guidance a worker needs.
- A stream or extract has a stale watermark or late corrections.
- A tested join held in one sample but fails on another window.
- A recipe needs more source privileges than the guide's intended audience has.
- Source metadata or examples themselves reveal sensitive information.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Source guidance MUST be scoped by source identity, environment,
  tenant or equivalent boundary, access role, and applicable definitions.
- **FR-002**: The worker MUST produce a reusable source skill with a concise
  entry guide and separate references for structure, meaning, joins and grain,
  time and freshness, and tested read recipes.
- **FR-003**: Each guidance assertion MUST identify whether it was declared,
  observed, tested on a stated version/window, or inferred.
- **FR-004**: Discovery and recipe validation MUST respect bounded query,
  time, and data-access allowances.
- **FR-005**: A guide MUST NOT contain credentials or raw sensitive records,
  and its metadata MUST remain within the authorized audience.
- **FR-006**: Source content MUST be treated as untrusted data; it MUST NOT
  grant access or override policy and worker instructions.
- **FR-007**: Candidate guidance MUST be validated before activation and
  activated as an immutable version.
- **FR-008**: Reuse MUST apply feature 015 FR-001's normative compatibility
  predicate; the inquiry MUST pin the skill version actually supplied.
- **FR-009**: The product MUST record supplied references and executed
  recipes separately; it MUST NOT claim to know whether the model understood
  every reference.
- **FR-010**: Changed guidance MUST invalidate affected facts without silently
  rewriting a prior guide or every unrelated fact.
- **FR-011**: Study-specific outcomes MUST remain separate from general source
  guidance, with provenance and exposure recorded whenever reused.

### Key Entities

- **Source identity**: The environment, role, access scope, and version context.
- **Source guide version**: A reviewed package of entry guidance, references,
  recipes, and validation evidence.
- **Guidance assertion**: A fact with source, status, scope, and validation time.
- **Usage record**: Which guide material was supplied or executed in an inquiry.
- **Invalidation**: The change that makes some guidance stale or revoked.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A second independent inquiry receives and uses the exact active
  guide version; the evidence trace names the supplied references and at
  least one recipe actually executed.
- **SC-002**: All seeded same-schema semantic, role, key, and freshness changes
  invalidate the affected guidance before unsupported reuse.
- **SC-003**: The activated fixture package contains zero credentials, raw
  sensitive rows, or untagged study-specific evaluation findings.
- **SC-004**: All injected source instructions remain inert, with zero policy
  or access changes caused by source text.
- **SC-005**: The compatible second inquiry's discovery-step count, counted by
  feature 015's work-ledger definition, is at most half the cold-source
  inquiry's count while both retain their required checks.
- **SC-006**: A source discovery fixture that exceeds its declared query, row,
  or elapsed-time budget stops further discovery, records the exhausted limit,
  and activates zero unvalidated guidance.
- **SC-007**: Each fixture guidance assertion records declared, observed,
  tested-on-version, or inferred provenance. Activation requires at least one
  independently replayed bounded read recipe under the intended grant;
  unvalidated guidance stays `candidate`.
- **SC-008**: The activated guide records source identity, environment,
  tenant, access role, and applicable definitions; reusing it under a
  mismatched tenant or role fails feature 015's compatibility predicate.
- **SC-009**: Adding an unused column leaves unrelated validated guidance
  active; a previously tested join that fails key/cardinality validation in
  the next window marks only affected recipe guidance `stale` before reuse.

## Assumptions

- The first source is a bounded read-only relational fixture; the guidance
  model also represents time windows needed by future recurring sources.
- Source owners may provide definitions; the worker does not infer authority
  from column names or value distributions alone.
- A guide is operational memory, not a mechanism for granting database or
  filesystem permissions.

**Proposal trace**: Slice C; §§6, 13–14; T17–T19, T25; invariants I08, I11,
I14.
