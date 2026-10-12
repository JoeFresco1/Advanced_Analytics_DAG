# Feature Specification: Inspect a Result and Its Claim

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Give users an answer-first report whose numbers and charts open exact evidence and whose limitations remain visible.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Follow a reported number (Priority: P1)

An analyst clicks a reported number and sees its claim, measured result,
checks, computation, transformation, and source version.

**Why this priority**: Evidence is useful only when the person consuming the
answer can inspect it.

**Independent Test**: Open a fixed report and follow every displayed metric
to a present matching result.

**Acceptance Scenarios**:

1. **Given** a supported value, **when** opened, **then** its unit,
   denominator, scope, checks, and data origin are visible.
2. **Given** a stale or missing result, **when** the report opens, **then**
   the value is visibly unsupported rather than shown as verified.

### User Story 2 - Understand the answer (Priority: P2)

The report leads with the answer and its limits; detailed runtime data is
available when requested.

**Why this priority**: A trace is not a substitute for a clear analytical answer.

**Independent Test**: Review a report with a negative finding, a chart, and
one disputed denominator.

**Acceptance Scenarios**:

1. **Given** a negative finding, **when** the report opens, **then** it states
   the comparison and uncertainty without relabeling it a pipeline failure.
2. **Given** a chart, **when** inspected, **then** aggregation, labels,
   source table, and uncertainty definition are available.
3. **Given** active markup or an out-of-root path in a generated artifact,
   **when** the viewer opens it, **then** the markup is inert and the path
   grants no access to the named file.
4. **Given** a reader with evidence-metadata access but no raw-source access,
   **when** the trace opens, **then** scoped metadata and limitations appear
   while raw data and restricted paths remain inaccessible.

### Edge Cases

- Narrative text swaps two metrics or rounds away a material difference.
- Generated HTML contains active markup or a path outside allowed artifacts.
- The reader lacks permission to access the source data.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The default report MUST state answer, population, period,
  material assumptions, uncertainty, and remaining limitations.
- **FR-002**: Each numerical assertion MUST bind to a result selector with
  value, unit, denominator, rounding, and scope.
- **FR-003**: Every result trace MUST reveal claim, checks, computation,
  transformation, and source version, and MUST render each link as exactly one
  of the trace-link states `resolved`, `broken`, `stale`, `redacted`, or
  `access_denied`. A link whose state cannot be determined MUST be rendered
  `broken`; `redacted` and `access_denied` MUST NOT be rendered as verified.
- **FR-004**: Chart traces MUST identify source aggregation, plotting choices,
  labels, and uncertainty meaning.
- **FR-005**: Report wording MUST distinguish prediction, association, and
  causation and reflect negative or inconclusive evidence accurately.
- **FR-006**: Untrusted report content MUST not execute instructions or grant
  access to undeclared artifacts.
- **FR-007**: The viewer MUST apply source access scope to raw data and
  restricted paths while showing permissible evidence metadata and limits.
- **FR-008**: The viewer MUST state whether its source restriction is enforced
  by the product surface alone or by an independent filesystem/source grant.
  It MUST NOT describe a locally readable source as sealed from a worker
  with shell access to those bytes.

### Key Entities

- **Report assertion**: A value or factual statement with a scoped claim.
- **Result selector**: The exact measured quantity and presentation rule.
- **Trace**: The user-visible chain to source and validation evidence.
- **Trace link state**: One of `resolved`, `broken`, `stale`, `redacted`,
  `access_denied` — the viewer's verdict on a single trace link.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Every fixture number opens a complete trace or a visible
  FR-003 link state, zero links remain in an undetermined state, each seeded
  broken, stale, redacted, and access-denied link renders its own state, and
  zero unsupported numbers appear as verified.
- **SC-002**: All seeded value, denominator, and unit swaps are detected
  before a supported report is displayed.
- **SC-003**: The report answers the fixture question on its first screen,
  with the full evidence reachable through one result selection.
- **SC-004**: Seeded script markup executes zero actions and seeded
  path-traversal references expose zero out-of-root bytes in the viewer.
- **SC-005**: The metadata-only reader fixture sees the scoped trace and zero
  raw-source values or restricted paths.

- **SC-006**: Every chart in the report fixture opens a trace naming its
  aggregation, axes, scale, filters, labels, uncertainty meaning, and source
  Result; a planted scale or filter change is visible in that trace.
- **SC-007**: Negative and inconclusive fixture results retain their status
  in the first-screen answer, and a predictive association fixture contains
  zero unsupported causal assertions.
- **SC-008**: The locally readable restricted-source fixture shows a
  workflow-level access label; the independently permissioned fixture shows
  its stronger source boundary, and neither viewer path discloses ungranted
  raw values.
- **SC-009**: The first-screen fixture answer shows population, period,
  material assumptions, uncertainty, and remaining limitations; omitting
  one required field prevents the report from being marked complete.
- **SC-010**: A planted rounding or scope mismatch in a numerical assertion
  is detected before supported display; the trace shows the exact selector,
  unrounded value, rounding rule, denominator, unit, and scoped population.

## Assumptions

- Readers may have evidence metadata access without raw-data access.
- Not every connective sentence requires a separate evidence object.

**Proposal trace**: §15; T01, T11, T22–T23, T60; I02, I08, I14.
