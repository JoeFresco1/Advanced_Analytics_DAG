# Feature Specification: Reconcile Recurring Data Windows

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Rerun a known analysis on a new bounded window and handle late corrections without silently changing old answers.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Compare a new window (Priority: P1)

An analyst sees a new measurement alongside the old one and knows whether
period, population, definition, and completeness permit comparison.

**Why this priority**: A metric trend is misleading when source meaning or
coverage changed.

**Independent Test**: Recur on a compatible window and one with a changed
definition; inspect both comparison states.

**Acceptance Scenarios**:

1. **Given** a finalized compatible window, **when** it runs, **then** a
   distinct result is linked to the earlier result without overwriting it.
2. **Given** changed definition or coverage, **when** periods are compared,
   **then** the mismatch is visible before an unqualified trend is reported.

### User Story 2 - Handle a correction (Priority: P1)

After a late event changes an earlier window, the analyst can find affected
results, claims, reports, skills, and use assessments.

**Why this priority**: Downstream users may have acted on the old version.

**Independent Test**: Insert a late event into a previously finalized fixture
window and inspect its dependency trail.

**Acceptance Scenarios**:

1. **Given** a corrected window, **when** it is acquired, **then** a new
   version and correction relationship appear and dependent claims are flagged.
2. **Given** a provisional window, **when** a report opens, **then** its
   completeness and lateness limits remain visible.
3. **Given** a second correction to the same window, **when** impact is
   assessed, **then** each version and all dependent record types named in
   FR-004 remain linked and their current validity is visible.
4. **Given** repeated inferential monitoring without a declared inspection
   rule, **when** a favorable look is selected, **then** the claim cannot be
   presented as a fresh independent confirmation; a declared sequential rule
   records and evaluates each look under that rule.

### Edge Cases

- An earlier window's data is no longer retained.
- The same window has two corrections.
- Event time and processing time differ materially.
- Repeated monitoring tempts unlimited fresh significance tests.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Every recurrence MUST create a new source and execution
  version and preserve earlier results.
- **FR-002**: Window identity MUST include boundaries, completeness,
  source position, lateness, and label maturity where relevant.
- **FR-003**: Each period comparison MUST record the canonical state
  `unassessed`, `comparable`, `qualified_comparison`, or `incomparable` using
  definition, population, coverage, weighting, and freshness. An unassessed
  or incomparable pair MUST NOT yield an unqualified change claim; a
  qualified comparison MUST show the adjustment and interpretation limit.
- **FR-004**: Corrections MUST traverse recorded dependencies to identify
  affected Results, Claims, Check Results, reports, answer cards, ledger
  entries, source-skill versions, Use Assessments, and Decision references.
  Each affected record MUST receive a canonical current-validity signal or a
  linked reassessment without erasing its historic version.
- **FR-005**: Historical answers MUST remain inspectable with current
  validity and reproducibility status.
- **FR-006**: Repeated inferential monitoring MUST use a declared inspection
  policy with planned look times and a valid sequential or multiplicity
  procedure. An undeclared additional look MUST record exposure and block an
  independent confirmatory label; the result may be reported as exploratory.
- **FR-007**: Window identity MUST retain event-time and processing-time
  boundaries separately when they differ, and a late event MUST use the
  event-time window for correction impact without erasing arrival history.

### Key Entities

- **Window version**: Bounded acquisition and completeness state.
- **Correction relationship**: New evidence replacing an earlier source view.
- **Dependency impact**: Downstream records requiring reassessment.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Zero prior result versions are overwritten across the fixture
  recurrence and correction.
- **SC-002**: Every seeded definition, coverage, and late-event change is
  visible before an unqualified comparison.
- **SC-003**: The correction fixture identifies all known dependent Results,
  Claims, Check Results, reports, answer cards, ledger entries, source-skill
  versions, Use Assessments, and Decision references; each has a visible
  current-validity signal or linked reassessment.
- **SC-004**: The undeclared repeated-look fixture produces zero independent
  confirmatory labels; the declared sequential fixture records every look
  against its locked inspection policy.
- **SC-005**: The delayed-event fixture preserves both event and processing
  times and links its correction to the affected historical window.
- **SC-006**: Each fixture Window identity records boundaries, completeness,
  source position, lateness, and applicable label maturity; removing a
  required component leaves the pair `unassessed`.
- **SC-007**: Frozen matching, adjusted-coverage, changed-definition, and
  missing-metadata pairs yield respectively `comparable`,
  `qualified_comparison`, `incomparable`, and `unassessed`; only the qualified
  pair shows its adjustment and bounded interpretation before a trend claim.
- **SC-008**: After correction and source retention loss, the historical
  answer remains inspectable with separate current-validity and reproduction
  states; neither is inferred from its old successful execution.
- **SC-009**: Two successive corrections to one window create two linked
  source versions and retain the original; dependency impact is recomputed
  for each version without dropping earlier affected records.

## Assumptions

- Initial recurrence uses bounded extracts or windows, not continuous
stream processing.
- Some historic source data may become unavailable under retention rules.

**Proposal trace**: §14; T18, T20, T22, T58; I07–I08.
