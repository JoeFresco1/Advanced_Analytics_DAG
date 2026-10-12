# Feature Specification: Find Past Analytical Answers

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Persist every completed or stopped inquiry as an answer card and recall relevant past answers during later work, including negative or inconclusive findings.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Find what the team learned (Priority: P1)

An analyst asks what was learned about churn last quarter. The project
library returns the relevant answer, its population, period, uncertainty,
and the exact evidence and files behind it, without requiring a filename.

**Why this priority**: A searchable answer matters more than remembering
which of dozens of spreadsheets happened to contain the final table.

**Independent Test**: Search a fixture library with 99 files, one supported
answer, one negative answer, and many intermediates.

**Acceptance Scenarios**:

1. **Given** the crowded fixture, **when** the analyst searches by question
   and period, **then** the intended answer card appears with evidence links.
2. **Given** a negative or inconclusive inquiry, **when** searched, **then**
   its honest conclusion is returned rather than hidden as a failed run.
3. **Given** many cards sharing keywords but not cohort or period, **when**
   ranked, **then** a scope-compatible card precedes keyword-only distractors;
   user pins cannot override access or scope compatibility.
4. **Given** an empty project or a question with no matching card, **when**
   searched, **then** the library says no matching answer and does not invent
   a conclusion from a file name.

### User Story 2 - Mark and revisit important work (Priority: P1)

The analyst can pin or annotate a useful answer. If its source is later
corrected, the card remains findable with a stale label and a link to the
newer assessment.

**Why this priority**: Importance is a user preference; currency is an
evidence property. Both need to survive across runs.

**Independent Test**: Pin a fixture answer, correct its source, rebuild the
search index, and inspect the card.

**Acceptance Scenarios**:

1. **Given** a pinned answer, **when** the catalog is rebuilt, **then**
   pin, annotation, and stable evidence links remain.
2. **Given** a source correction, **when** the card is retrieved, **then**
   it is labeled stale or superseded without losing the historic answer.
3. **Given** a user says “remember this analysis,” **when** the inquiry is
   closed, **then** its answer card is pinned with the scoped evidence and no
   generic skill is created solely from that request.

### User Story 3 - Recall an answer without being prompted (Priority: P1)

Months later, an analyst asks a related question without mentioning the old
analysis. The worker sees the relevant answer during orientation, checks its
population and current evidence, and either builds on it or explains why it
cannot be reused.

**Why this priority**: A searchable archive still loses value when nobody
remembers to search it.

**Independent Test**: Complete an inquiry, then start a later related inquiry
with no hint that the prior analysis exists; repeat with a different cohort.

**Acceptance Scenarios**:

1. **Given** a compatible prior answer, **when** the later inquiry starts,
   **then** the worker receives its scoped card and evidence links and records
   whether it used the finding.
2. **Given** a superficially similar answer from another cohort or an outdated
   source, **when** it is retrieved, **then** the worker identifies the mismatch
   and does not present the old claim as a current answer.
3. **Given** the earlier worker omitted its summary, **when** that inquiry
   closes, **then** the controller saves a minimal card from committed evidence
   with the missing interpretation or incomplete status visible.
4. **Given** the analyst opens the answer, **when** they inspect its history,
   **then** they can see the linked timeline of attempts, including methods
   tried, non-findings, failed checks, and reasons for the next action.

### Edge Cases

- One inquiry has several competing claims rather than one simple conclusion.
- The original evidence has been redacted or is no longer available.
- A previous holdout result is retrieved into a new inquiry.
- Two answer cards refer to the same metric but different cohorts.
- The worker exits or crashes after committing results but before writing prose.
- Many superficially related cards compete for limited context.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each completed or stopped inquiry MUST yield one scoped
  answer card with question, answer or non-answer, key claims, period,
  population, evidence state, and links to its artifacts; card persistence MUST
  be part of controller finalization, not an optional worker action.
- **FR-002**: Search MUST support question text plus filters for source,
  metric, period, claim state, and user pins.
- **FR-003**: The library MUST separate curated answer cards from the full
  artifact inventory so intermediates remain findable without crowding answers.
- **FR-004**: Pins and annotations MUST express user importance without
  changing statistical support or claim scope.
- **FR-005**: Corrections, supersession, redaction, and unavailable evidence
  MUST update the card's canonical current-validity and availability fields
  without erasing its history.
- **FR-006**: Retrieving a past outcome into a worker's context MUST record
  its provenance and any evaluation exposure it introduces.
- **FR-007**: Search results MUST respect the access scope of source records.
- **FR-008**: The short answer story MUST identify its selected findings and
  selection reasons; other results MUST remain linked without being presented
  as equally important or as better supported than their evidence allows.
- **FR-009**: When a worker summary is missing, the controller MUST create a
  minimal card from committed records, mark interpretation missing or work
  incomplete, and avoid inventing a conclusion.
- **FR-010**: New-inquiry orientation MUST search the answer library, rank a
  bounded set of relevant cards, and supply match reasons, scope, validity,
  and evidence links to the worker without a user reminder.
- **FR-011**: The worker MUST record whether it used a surfaced finding or why
  it rejected it; loading an outcome MUST preserve exposure history.
- **FR-012**: The project view MUST surface three groups without a search
  query: `recent`, the N most recent cards ordered by revision time with N
  declared configuration recorded alongside the view; `pinned`, every card
  carrying a durable pin event; and `unresolved`, every card whose inquiry
  outcome is `needs_input`, `budget_exhausted`, or `execution_blocked`, or
  any of whose claims has evidence state `inconclusive` or `conflicting`.
  The view MUST label which group a card appears in, and a card may appear in
  more than one.
- **FR-013**: Each answer card MUST link to the inquiry's work-ledger timeline
  so the user can inspect what was tried and why work continued or stopped.
- **FR-014**: Retrieval MUST filter access before ranking; classify a card as
  `direct_answer` only when question intent and metric match and population,
  period, source meaning, and current evidence are compatible. Cards that
  share words but differ materially in scope MUST be labeled `related`, not
  direct answers. Pins and recency MAY break ties among equally relevant
  cards but MUST NOT override validity, scope, or access. Known structured
  fields are checked deterministically; inferred intent and meaning MUST
  retain the worker's reason and attribution. Unknown compatibility MUST NOT
  become a `direct_answer` match. Per-card classification is
  `direct_answer`, `related`, or `not_relevant`.
- **FR-015**: Search MUST return an explicit `no_match` outcome when the corpus
  is empty or no accessible card meets the direct-answer relevance rule;
  otherwise the query outcome is `direct_answer_found`. Related work MAY be
  shown separately with its mismatch reason.
- **FR-016**: The answer index MUST be rebuildable from committed inquiry
  records and durable curation events without losing stable card IDs, pins,
  access scope, or current-validity status.
- **FR-017**: New-inquiry orientation MUST call feature 023's prior-attempt
  match once, then rank feature 020 answer cards from feature 019's
  access-filtered index. The retrieval event MUST record the query, candidate
  set, ranked cards, mismatch reasons, and exposure. A worker-authored
  semantic match MUST remain attributed and cannot override a known scope
  mismatch.

### Key Entities

- **Answer card**: A concise, versioned story of a question and its findings.
- **Curation record**: Pin, annotation, grouping, or supersession decision.
- **Retrieval event**: What past material entered a worker's context.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: An analyst finds the intended answer in the 99-file fixture
  using the question and period, without knowing any filename.
- **SC-002**: Negative and inconclusive fixture answers are retrievable with
  their uncertainty, each card's displayed status equals its
  controller-recorded inquiry outcome, and zero cards labeled `failed` or
  equivalent appear where the recorded outcome is `answered`,
  `answered_with_uncertainty`, or `insufficient_evidence`.
- **SC-003**: Pins, annotations, and stable references survive a full index
  rebuild; corrected cards show current validity without losing history.
- **SC-004**: A user without access to a restricted source receives no
  restricted title or content in search results or worker context.
- **SC-005**: In the crowded fixture, the answer card highlights the
  question-relevant supported finding, keeps discarded exploratory outputs
  in the inventory, and shows why the finding was selected.
- **SC-006**: Every completed or stopped fixture inquiry has a durable card,
  including the worker-without-summary fixture, after a simulated restart.
- **SC-007**: A later related inquiry with no user hint surfaces the compatible
  prior answer and records use; an incompatible cohort or stale source is
  flagged and its claim is not reused as current evidence.
- **SC-008**: Opening the fixture answer shows every admitted attempt in its
  ledger timeline with the method, outcome state, and evidence links.
- **SC-009**: On a frozen fixture of at least 150 cards and 20 annotated
  queries, including same-keyword wrong-cohort and wrong-period distractors,
  the primary relevant card appears in the top three for at least 18 queries.
  The evaluation reports Hit@3 as qualifying queries divided by 20, and
  every returned card includes its relevance and mismatch reason.
- **SC-010**: Five frozen no-match queries, including an empty corpus, yield
  zero false direct answers; restricted cards never appear in any result.
- **SC-011**: The project-home fixture displays its FR-012 `recent`, `pinned`,
  and `unresolved` groups with stable links and group labels, including a
  negative finding in `unresolved` and an inquiry stuck at `needs_input`.
- **SC-012**: The same-keyword wrong-cohort fixture records deterministic
  scope filtering, the worker's attributed intent judgment, one prior-attempt
  match call, and one retrieval event; zero known-incompatible cards are
  labeled direct answers.
- **SC-013**: Question text plus each source, metric, period, Claim-state,
  and pin filter returns the preannotated accessible cards; intermediates
  remain in the artifact inventory rather than the curated answer list.
- **SC-014**: Pinning or annotating a card changes neither its Claim evidence
  state nor scope. A worker receiving a prior card records use or a specific
  rejection reason, and the retrieval event preserves any outcome exposure.
- **SC-015**: Every finalized fixture card contains the original question,
  scoped answer or non-answer, selected Claims, period, population, evidence
  state, current validity, and artifact links; a missing worker summary uses
  the deterministic fallback without inventing an interpretation.

## Assumptions

- A card may have multiple claims or say insufficient evidence.
- User pins affect ordering only within equally relevant, accessible cards;
  ranking quality is evaluated without pins.
- The first library is project-scoped; cross-project discovery can follow.

**Proposal trace**: §§15, 21; T31, T35–T37, T39–T42, T51,
T56–T57; I17–I18, I20.
