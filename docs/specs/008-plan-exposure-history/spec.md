# Feature Specification: Preserve Plans and Outcome Exposure

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Show what the worker planned, what outcomes it saw, and whether a later choice was planned.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Inspect the inquiry path (Priority: P1)

An analyst sees the original question, exploratory observations, directed
plan, and exposure history behind a claim.

**Why this priority**: A computation can be misrepresented as confirmatory if
its method was selected after viewing outcomes.

**Independent Test**: Explore development data, lock a comparison, and change
the method after viewing results.

**Acceptance Scenarios**:

1. **Given** an outcome-informed change, **when** the worker proceeds,
   **then** a new plan links to the earlier plan and preserves exposure.
2. **Given** a predeclared resampling step, **when** it runs, **then** it stays
   within the planned family rather than receiving a false post-observation label.

### User Story 2 - Interpret later confirmation (Priority: P2)

A reviewer can see that development work informed a plan while fresh data
evaluated it, without claiming the worker had no prior knowledge.

**Why this priority**: Pre-specification is relative to a comparison and
evidence boundary.

**Independent Test**: Evaluate a development-informed plan on fresh data.

**Acceptance Scenarios**:

1. **Given** a later evaluation, **when** its claim appears, **then** both
   exploratory origin and fresh evaluation evidence are visible.
2. **Given** possible out-of-tool access, **when** exposure is summarized,
   **then** unknown access is not represented as no access.

### Edge Cases

- A source skill contains an earlier outcome and is loaded into the inquiry.
- A code bug is repaired without changing the comparison.
- A different harness resumes from a shortened summary.
- A human approves post-observation work for exploratory use.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each inquiry MUST retain original wording, operational
  interpretations, and every plan revision.
- **FR-002**: A directed plan MUST declare population, target, comparison,
  partitions, search space, stopping rule, and required checks.
- **FR-003**: Exposure MUST identify actor, material seen, source or
  partition, time, and observation or self-report status.
- **FR-004**: An outcome-informed substantive change MUST create a new plan
  without erasing earlier evidence or exposure.
- **FR-005**: Planned searches and post-observation choices MUST retain their
  distinct historical labels.
- **FR-006**: Review MUST NOT convert historical exposure into pre-specification.
- **FR-007**: Restarted workers MUST inherit enough history to preserve labels.
- **FR-008**: A change to population, target, method, partition design,
  search space, or stopping rule after outcome inspection MUST be classified
  as substantive and produce a new Plan revision. A code correction MUST
  produce a new Execution and code digest; it changes the Plan only if one of
  those analytical choices changes. The controller MUST record why the
  change was classified as substantive or implementation-only.
- **FR-009**: The controller MUST assign the canonical plan interpretation
  status from lock time, declared inspection policy, and recorded exposure.
  The worker MUST explain any proposed substantive-versus-code-only
  classification; the controller mechanically checks changed locked fields
  and records the worker's reason. Unresolved semantic classification MUST
  remain attributed and cannot qualify for `planned_confirmatory`.

### Key Entities

- **Question**: Original wording and scoped interpretation.
- **Plan revision**: Locked comparison and its predecessor.
- **Exposure record**: Known, reported, or unknown access to outcomes.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Every injected outcome-informed revision retains earlier
  exposure and receives a distinct plan identity.
- **SC-002**: Declared search candidates produce zero false post-observation
  flags in the reference fixture.
- **SC-003**: No review or restart removes a recorded exposure or changes a
  historical plan label.
- **SC-004**: Each seeded population, method, split, search-space, and
  stopping-rule revision after observation creates a linked Plan; a
  code-only bug fix creates a new Execution without falsely rewriting the
  prior Plan.
- **SC-005**: Locked-before-exposure, unplanned, post-observation revision,
  and unknown-exposure fixtures receive respectively `planned_confirmatory`,
  `exploratory`, `outcome_informed`, and `exposure_unknown`; no worker restart
  or review turns the latter two into planned confirmation.
- **SC-006**: The fixture inquiry retains its original wording and every
  operational interpretation. Its directed Plan includes every required
  FR-002 field, and each exposure record identifies actor, material, source
  or partition, time, and whether access was observed or self-reported.

## Assumptions

- Out-of-tool access cannot be disproven by local logs.
- Development-informed plans can be tested on fresh data with both facts shown.

**Proposal trace**: §§4–5, 8, 13; T06–T07, T09, T25, T61; I05, I08.
