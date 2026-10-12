# Feature Specification: Reuse and Refresh a Source Skill

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Supply a compatible skill to a later worker, show actual consumption, and invalidate changed guidance without rewriting past analyses.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Reuse tested source knowledge (Priority: P1)

A later analyst asks a second question about the source. The worker receives
the right skill version and uses a tested reference or recipe after a cheap
compatibility check.

**Why this priority**: A written skill has value only when it changes later
work while preserving the required checks.

**Independent Test**: Run two independent questions on one source and compare
cold discovery with the second inquiry's recorded consumption.

**Acceptance Scenarios**:

1. **Given** a compatible source and role, **when** a new inquiry starts,
   **then** the supplied skill version and references are recorded.
2. **Given** a recipe used by that inquiry, **when** its trace is opened,
   **then** the executed recipe is distinguishable from merely supplied text.

### User Story 2 - Detect semantic drift (Priority: P1)

The analyst knows when a changed key, unit, category definition, access role,
or freshness assumption makes parts of a skill unsafe to reuse.

**Why this priority**: A schema fingerprint alone cannot protect meaning.

**Independent Test**: Change a fixture unit while keeping the schema, then
repeat with an unused column added.

**Acceptance Scenarios**:

1. **Given** a changed unit with unchanged schema, **when** reuse is checked,
   **then** affected guidance is `stale` and cannot guide current work. If a
   validated replacement exists, it is pinned and used; otherwise the answer
   states the unresolved assumption and does not claim current validity.
2. **Given** an unused new column, **when** reuse is checked, **then**
   unrelated guidance remains available.

### Edge Cases

- A skill carries holdout findings through a shared summary.
- Access rights narrow after a skill was activated.
- An old run pins a now-revoked skill version.
- The model is shown a reference but never follows its recipe.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Source-skill reuse MUST apply one normative compatibility
  predicate across source identity, environment or tenant, current grant and
  audience, lifecycle status, schema and key/grain meaning, units and
  timezone, category and metric definitions, source version, watermark or
  freshness, and recipe privileges. A required unknown or mismatch MUST
  prevent silent current guidance; the worker may receive a scoped historical
  reference with its limitation and exposure recorded.
- **FR-002**: Each inquiry MUST pin the exact supplied skill version and
  distinguish supplied references from executed recipes.
- **FR-003**: Changed assumptions MUST mark only affected guidance `stale`
  and create a new version on refresh. `revoked` is reserved for the
  authority-withdrawal transition owned by feature 022.
- **FR-004**: Old runs MUST retain their original skill versions and receive
  an explicit current-validity signal when evidence changes.
- **FR-005**: Study-specific outcome summaries MUST remain separate from
  general guidance, and loading them MUST record exposure.
- **FR-006**: A skill MUST NOT grant source rights beyond the worker's actual
  access identity.

### Key Entities

- **Skill version**: Immutable scoped source guidance.
- **Usage record**: References supplied and recipes executed.
- **Invalidation**: Changed fact, affected guidance, and dependent inquiries.
- **Discovery step**: One admitted bounded profiling, metadata-inspection, or
  schema-exploration task recorded in the work ledger. Counting is by ledger
  entries, not by tool calls, so a step cannot be hidden by batching.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A second inquiry records one exact skill version and at least
  one executed recipe, and its discovery-step count is at most half the cold
  inquiry's count on the same fixture.
- **SC-002**: Every seeded same-schema meaning or role change makes affected
  guidance stale; an unused-column addition leaves unrelated guidance usable.
- **SC-003**: Zero prior outcomes enter a new inquiry without an exposure record.
- **SC-004**: After a unit change and refresh, the earlier inquiry still pins
  its original skill version and historical result, while current retrieval
  marks that version `stale` and supplies only the validated replacement.
- **SC-005**: A worker whose source rights narrowed receives zero recipes or
  references requiring the former access, even if an old skill remains active
  for other roles.
- **SC-006**: Each seeded tenant, key/grain, unit, timezone, metric-definition,
  watermark, and grant mismatch is caught by the same compatibility predicate;
  an unrelated added column leaves otherwise compatible guidance available.
- **SC-007**: A historical inquiry still pins a subsequently `revoked`
  version, while a new inquiry receives zero current guidance from it. A
  supplied but unexecuted recipe is recorded as supplied only, never as an
  executed use.

## Assumptions

- The source owner or trusted metadata provides authoritative meanings.
- Supplied references prove delivery to the worker, not model comprehension.

**Proposal trace**: §§13–14; T18–T19, T25; I05, I08, I11.
