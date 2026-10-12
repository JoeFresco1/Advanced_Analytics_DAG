# Feature Specification: Trace Data Identity and Measurement

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Make source versions, joins, grain, denominators, and time boundaries inspectable before choosing a method.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Know which data was measured (Priority: P1)

An analyst can identify the source version, period, eligible population,
observation grain, and exclusions behind a displayed denominator.

**Why this priority**: An accurate calculation on the wrong population is an
incorrect answer to the user's question.

**Independent Test**: Measure a fixed customer fixture and compare the visible
lineage to its known acquisition and transformation history.

**Acceptance Scenarios**:

1. **Given** a fixed extract, **when** a result is shown, **then** source
   identity, version, grain, time cutoff, and denominator are traceable.
2. **Given** a duplicate join or unexplained exclusion, **when** measurement
   proceeds, **then** changed row and entity counts appear and supported
   presentation of the affected Claim is blocked.

### User Story 2 - Compare acquisitions (Priority: P2)

An analyst can tell whether two acquisitions are comparable and replayable.

**Why this priority**: A query timestamp alone does not identify changing data.

**Independent Test**: Acquire two versions with the same schema but different
content or source consistency guarantees.

**Acceptance Scenarios**:

1. **Given** two changing-source reads, **when** compared, **then** their
   versions and consistency guarantees are shown separately.
2. **Given** unavailable original data, **when** replay is requested, **then**
   availability is reported accurately while permitted evidence remains.

### Edge Cases

- A join legitimately increases rows but not distinct customers.
- Currency, timezone, key meaning, or category definitions change.
- A source provides a query time but no repeatable snapshot.
- An event arrives late or a target label is immature.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each acquisition MUST identify source, access scope, retrieval
  scope, time, consistency guarantee, and retained or retrievable version.
- **FR-002**: Each transformation MUST identify input and output versions,
  grain, entity keys, counts, exclusions, joins, and time cutoffs.
- **FR-003**: A result MUST expose its denominator, units, and eligible
  population through this lineage.
- **FR-004**: The product MUST distinguish justified row multiplication from
  unexplained expansion and show affected entity counts. A transformation
  MUST declare expected key cardinality and any intentional change in grain
  before execution; expansion is unexplained when observed row/entity changes
  exceed that declaration or the declaration is missing. The affected Claim
  MUST NOT be presented as supported until the mismatch is resolved by a new
  validated transformation revision.
- **FR-005**: Unknown consistency or unavailable replay MUST remain explicit.
- **FR-006**: Changed source meaning MUST be visible even with unchanged schema.
- **FR-007**: A legacy imported file with no preexisting cardinality contract
  MUST retain `unknown` grain/cardinality provenance. Feature 019 may catalog
  it without inventing a prior declaration. Before a new supported
  transformation consumes it, the worker MUST declare expected input grain
  and key cardinality, then validate observed counts; unresolved mismatch
  blocks the dependent Claim under FR-004.

### Key Entities

- **Source version**: What was read and what consistency was guaranteed.
- **Transformation**: How population, grain, units, and time scope changed.
- **Measurement scope**: Denominator, grain, and units of a result.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Every fixture denominator resolves to a source version and
  complete transformation path.
- **SC-002**: Both seeded duplicate-join and hidden-exclusion faults produce
  visible count/cardinality discrepancies and block supported presentation;
  a declared, validated one-to-many join remains usable.
- **SC-003**: No non-repeatable source fixture receives an exact-snapshot label.
- **SC-004**: A same-schema unit or key-meaning change is detected and linked
  to affected measurements before a supported comparison is returned.
- **SC-005**: An undeclared legacy import is cataloged with unknown
  cardinality; a new transformation using it gets no supported Claim until
  its declared grain and key counts pass validation.
- **SC-006**: Each acquisition fixture records source identity, grant,
  retrieval scope and time, consistency guarantee, and replayable version or
  explicit `unknown`. The unavailable-replay fixture never receives a
  reproducible-snapshot claim.
- **SC-007**: Seeded currency, timezone, key-meaning, and category-definition
  revisions remain visible even when schema bytes are unchanged; affected
  measurements cannot be compared as though meaning were unchanged.
- **SC-008**: A late event retains its source and event-time provenance; an
  immature target label is excluded or explicitly marked unavailable with
  the affected cohort count before a supported transformation is returned.

## Assumptions

- Initial sources are bounded files or extracts; source adapters later add
  their actual consistency guarantees.
- A count change is evidence to explain, not automatically a failure.

**Proposal trace**: §§5–6, 11, 14; T05; I07–I08.
