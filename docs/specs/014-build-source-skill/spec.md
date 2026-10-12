# Feature Specification: Build a Source Skill

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Discover a frequently used data source and create a scoped, validated skill with references and tested read recipes.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Learn an unfamiliar source (Priority: P1)

An analyst authorizes a source; the worker produces a short guide, detailed
references, and read recipes that explain tables, grain, joins, definitions,
time behavior, and known pitfalls.

**Why this priority**: Later workers need reusable knowledge instead of
repeating expensive discovery.

**Independent Test**: Run bounded discovery on a fixed source and validate
the generated assertions and recipes against it.

**Acceptance Scenarios**:

1. **Given** an unfamiliar source, **when** discovery ends, **then** a
   candidate skill identifies scope, structure, meaning, and tested access.
2. **Given** a join seen only in a sample, **when** documented, **then** its
   window and evidential status are visible rather than called a universal rule.
3. **Given** an unknown definition or a conflict with an owner definition,
   **when** the guide is drafted, **then** the unknown stays explicit, the
   conflict is linked to the authority, and neither is activated as fact.

### User Story 2 - Keep untrusted source text inert (Priority: P2)

Source comments and examples cannot redefine the worker's instructions,
access, or policies.

**Why this priority**: Persistent source skills become privileged context for
future inquiries.

**Independent Test**: Seed metadata with an instruction to skip checks and
inspect the candidate skill and next worker context.

**Acceptance Scenarios**:

1. **Given** an instruction in source metadata, **when** the worker reads it,
   **then** it remains source content and no check or access policy changes.
2. **Given** a credential or sensitive row in a candidate, **when** the skill
   is assessed, **then** activation is refused until removed or scoped safely.

### Edge Cases

- Identical table names have different meaning across environments.
- A source definition is unknown or conflicts with an owner definition.
- A test recipe is unbounded or requires stronger access than its audience.
- Metadata itself is sensitive to a broader audience.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: A source skill MUST state source identity, environment,
  audience, role, and applicable schema and meaning scope.
- **FR-002**: It MUST include concise entry guidance, on-demand references,
  and tested, bounded read recipes.
- **FR-003**: Every assertion MUST state whether declared, observed, tested
  on a named version/window, or inferred; MUST carry the provenance reference
  it came from and a validation time; and, when its status is tested, the
  exact dataset version and window. An assertion missing any of those fields
  MUST NOT be rendered as guidance.
- **FR-004**: Candidate recipes MUST be tested under the intended access
  scope before activation.
- **FR-005**: Source text MUST remain untrusted and MUST NOT change access or
  instructions.
- **FR-006**: Skills MUST NOT include credentials, raw sensitive records, or
  untagged study-specific evaluation outcomes.
- **FR-007**: New guidance MUST enter the canonical `candidate` state; an
  unknown definition MUST remain unknown, and a conflicting definition MUST
  retain the named authority and block activation of the conflicting claim
  until the owner resolves it.
- **FR-008**: Skill metadata and references MUST use the source audience's
  access scope; a broader audience MUST NOT receive sensitive metadata even
  when raw rows are excluded.

### Key Entities

- **Source skill**: Scoped guidance and references for later workers.
- **Assertion**: A fact with provenance, status, and validation time.
- **Recipe**: Bounded read procedure tested in a named context.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: The fixture skill contains tested guidance for every key, grain,
  join, definition, and time boundary inside the applicable schema and meaning
  scope declared by FR-001, with no invented authority and with provenance
  and a validation time present on every assertion.
- **SC-002**: Zero injected source instructions alter checks, permissions,
  or governing worker instructions.
- **SC-003**: The activated fixture package contains zero credentials, raw
  sensitive rows, or untagged study outcomes.
- **SC-004**: The seeded unknown and owner-conflicting definitions yield zero
  automatically active assertions; each retains its uncertainty or conflict
  and authority reference.
- **SC-005**: A narrower-role fixture receives zero sensitive table or column
  metadata from a candidate skill intended for a more privileged audience.
- **SC-006**: The fixture skill has source, environment, role, audience,
  schema and meaning scope, an entry guide, on-demand references, and a
  bounded recipe tested under that grant. Every assertion names its
  declared, observed, tested-on-version, or inferred provenance; an untested
  recipe cannot be activated.
- **SC-007**: An unbounded recipe and one requiring a grant beyond the
  intended audience remain `candidate`; neither runs under that audience or
  appears as active guidance.

## Assumptions

- The first source is a bounded read-only relational fixture.
- Publishing a skill alone does not prove it is later reused; feature 015
  tests that consumption.

**Proposal trace**: §13; T17, T25; I08, I11.
