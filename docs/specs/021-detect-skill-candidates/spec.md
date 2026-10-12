# Feature Specification: Detect Reusable Skill Candidates

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Notice when a successful procedure, source discovery, correction, or business rule is worth preserving without requiring the user to say “create a skill.”

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Preserve a method the user values (Priority: P1)

After an analyst says they are happy with how a completed analysis was
performed, the system proposes a reusable method skill, with the reason,
example, scope, and tests visible for review.

**Why this priority**: The user should not need a separate meta-instruction
to preserve useful work.

**Independent Test**: Complete a validated analysis, respond “I am happy
with how we did this,” and inspect the memory checkpoint.

**Acceptance Scenarios**:

1. **Given** that feedback, **when** the inquiry settles, **then** one
   method candidate is proposed without requiring the phrase “make a skill.”
2. **Given** the same procedure proposed twice, **when** candidates are
   compared, **then** they are deduplicated or linked as revisions.

### User Story 2 - Notice repeated work (Priority: P1)

Across separate inquiries, the worker repeatedly reconstructs a validated
source mapping, business calculation, or analysis recipe. The system raises
a scoped candidate even without direct praise.

**Why this priority**: Repetition is evidence of future reuse value and a
source of wasted effort.

**Independent Test**: Replay two independent inquiries using the same tested
procedure and inspect the capture reason.

**Acceptance Scenarios**:

1. **Given** repeated validated work, **when** the second inquiry completes,
   **then** one candidate records both uses and expected reuse value.
2. **Given** a striking unvalidated result, **when** capture runs, **then**
   significance or model enthusiasm alone cannot activate a skill.

### Edge Cases

- The user praises the answer but not the method or rule behind it.
- A business calculation conflicts with an owner-curated definition.
- A prior holdout outcome appears in the proposed skill body.
- Candidate generation would exceed the inquiry's memory allowance.
- A procedure depends on one source role or time period only.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Capture evaluation MUST run at bounded checkpoints after a
  recorded inquiry outcome or a recorded user reaction, without requiring a
  special command. A checkpoint without either recorded event MUST NOT
  produce a candidate.
- **FR-002**: Candidate triggers MUST include user endorsement of the method
  or rule rather than answer alone; the same procedure validated in two
  independent inquiries; discovery exceeding a predeclared query or elapsed
  time threshold; and a correction whose validation and later reuse are
  recorded. Each triggered candidate MUST name the observed condition and
  its threshold or evidence IDs.
- **FR-003**: Each proposal MUST state its trigger, evidence references,
  skill type, intended audience, scope, expected reuse, and validation gaps.
- **FR-004**: The system MUST distinguish a result worth remembering from a
  reusable instruction; study-specific findings belong in answer cards.
- **FR-005**: Candidates MUST be deduplicated or linked as revisions, with
  a visible reason for proposing or suppressing each one.
- **FR-006**: Statistical significance, model confidence, file count, or
  user praise alone MUST NOT establish a general business-rule authority.
- **FR-007**: Candidate generation MUST obey a fixed time and work allowance.
- **FR-008**: A candidate that conflicts with a current owner-curated
  definition MUST name that owner and remain a candidate for feature 022's
  authority decision. Candidate detection MUST NOT activate guidance or
  appoint an authority.
- **FR-009**: Candidate detection MUST be evaluated against feature 018's
  pre-frozen capture fixture, with an adjudicated positive or negative
  proposal label for every signal and a policy version. Candidate precision
  MUST count correctly proposed positive signals over all proposed signals;
  candidate recall MUST count correctly proposed positive signals over all
  positive signals. Duplicate proposals count as incorrect proposals.

### Key Entities

- **Capture signal**: Recorded event suggesting future reuse value.
- **Skill candidate**: Proposed source, method, or business-rule guidance.
- **Suppression decision**: Why a proposal was deduplicated or not made.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Natural-language approval of a completed method produces
  exactly one candidate with a traceable trigger and evidence.
- **SC-002**: Repeated validated work across two inquiries produces one
  deduplicated candidate without an explicit skill request.
- **SC-003**: Unvalidated and merely significant fixture results produce
  zero activation-eligible method or rule candidates; answer-only praise
  produces an answer-card annotation and zero method candidates.
- **SC-004**: Candidate capture terminates within its declared time and work
  allowance in a repeated-trigger fixture, without skipping final reporting.
- **SC-005**: A conflicting-rule fixture produces one scoped candidate with
  the existing definition and configured owner linked; feature 021 performs
  zero activation transitions.
- **SC-006**: A predeclared costly-discovery threshold and a tested durable
  correction each produce a candidate with trigger, threshold or evidence
  IDs, type, scope, audience, expected reuse, and validation gaps; answer-only
  praise does not produce a method candidate.
- **SC-007**: Repeating an identical capture signal creates one candidate
  and a visible suppression reason; a changed procedure creates a linked
  candidate revision rather than overwriting the first.
- **SC-008**: A candidate body containing a prior holdout outcome is kept as
  a study-specific reference with exposure provenance, not an unscoped
  method instruction. A procedure proven only for one source role or period
  records that exact scope and is not proposed for broader use.
- **SC-009**: A frozen 48-signal fixture contains eight positive examples
  each of method endorsement, repeated validated work, costly discovery,
  and validated correction, plus eight negative answer-only praise signals
  and eight negative unvalidated-significance signals. Labels and policy
  version are locked before execution. The report shows proposal IDs,
  true-positive count, total-proposal count, positive-signal count,
  precision, recall, and duplicate-proposal count. The initial capture
  profile meets precision at least 0.80 on at least ten proposals, recall
  at least 0.80 on all 32 positive signals, and zero duplicate proposals;
  missing labels or denominators fail the profile.

## Assumptions

- The capture policy proposes candidates; feature 022 decides activation.
- User satisfaction is a preservation signal, not proof that a method
  generalizes or that a business rule has organizational authority.

**Proposal trace**: §21; T32–T34, T38, T40; I16–I17.
