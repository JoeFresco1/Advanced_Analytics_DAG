# Feature Specification: Record and Reuse Analysis History

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Give every admitted analytical attempt a readable, evidence-linked ledger entry and surface prior attempts before repeating work.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - See what we did (Priority: P1)

An analyst opens a question and sees a timeline of analyses: what was tried,
on which data, what came out, what failed or remained uncertain, and why the
worker continued or stopped. A short row links to the detailed evidence.

**Why this priority**: A final answer alone does not preserve the work that
led to it, especially abandoned branches and negative results.

**Independent Test**: Execute a supported comparison, a null result, and a
failed check, then interrupt the worker before it writes final prose.

**Acceptance Scenarios**:

1. **Given** those attempts, **when** the question timeline opens, **then**
   each attempt has a readable entry with question, method, data version,
   result or absence, status, next action, and evidence links.
2. **Given** the worker omitted its summary, **when** the controller recovers,
   **then** the entry states only committed facts and labels interpretation
   missing rather than inventing a conclusion.
3. **Given** an inconclusive or failed attempt, **when** the ledger is searched,
   **then** it remains visible as work done and is not labeled as a supported
   answer.

### User Story 2 - Avoid an accidental rerun (Priority: P1)

Months later, a worker proposes the same analysis. The prior attempt appears
before execution, so it can reuse valid evidence or record a concrete reason
for repeating it. A newer data window remains a legitimate new analysis.

**Why this priority**: Searchable history only saves effort if it participates
in planning and does not confuse replication with accidental repetition.

**Independent Test**: Propose an exact repeat on the same snapshot, then a
similar analysis on a newer window and a different cohort.

**Acceptance Scenarios**:

1. **Given** an exact valid prior computation, **when** the worker proposes
   the same analysis, **then** it sees the prior outcome before execution and
   records reuse or a deliberate rerun reason.
2. **Given** a new period or cohort, **when** the worker proposes analysis,
   **then** the prior entry is shown as related, the difference is visible,
   and the new execution is allowed with a comparison link.
3. **Given** a stale, failed, or inconclusive prior attempt, **when** it is
   surfaced, **then** it cannot be silently reused as supported evidence.

### Edge Cases

- Two users phrase the same operational question differently.
- Two analyses share a name but use different denominators or source roles.
- One plan contains several executions or candidate models.
- An infrastructure retry reruns the same command without changing the
  analytical question or plan.
- A source is redacted after the ledger entry was written.
- A dimension needed for exact-match assessment is unknown or unavailable.
- A prior evaluation outcome is loaded during a new inquiry.
- The search index is lost after durable attempt records are committed.
- The ledger has no attempts yet, or none are visible under the worker's
  access scope.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each admitted analytical attempt MUST have a durable identity
  and an append-only sequence of committed events from which the readable
  current ledger projection is rebuilt; later facts MUST create a linked
  revision rather than editing historical attempt facts in place;
  shell commands and infrastructure retries MUST NOT become separate
  analytical attempts solely because they executed again.
- **FR-002**: A ledger entry MUST show question, operational scope, population,
  period, data and transformation versions, method and parameters, execution
  state, results or explicit absence, evidence state, limitations, next-action
  rationale, and links to authoritative records.
- **FR-003**: Controller-derived facts and worker-authored interpretation MUST
  be distinguishable; missing interpretation MUST remain missing.
- **FR-004**: Failed, null, inconclusive, stopped, and superseded attempts MUST
  remain searchable and keep their historic status and revisions.
- **FR-005**: New-inquiry orientation and plan admission MUST search accessible
  prior attempts using question meaning and structured scope, then identify
  exact computation matches separately from related inquiries. Feature 023
  returns the match assessment to feature 020; it MUST NOT independently
  rank answer cards or supply prior outcomes to the worker. An empty ledger,
  or one with no attempt visible under the worker's access scope, MUST return
  an explicit no-prior-attempts outcome rather than an error, an empty
  result set that reads as a clear answer, or a duplicate block.
- **FR-006**: An exact-match decision MUST compare input versions, source
  role and semantic version, population, denominator, target,
  transformations, method, code, parameters, and evaluation contract;
  every listed dimension MUST be known and equal. If any dimension differs,
  the prior attempt is `related` only when the scoped operational question
  still matches and the known change is cohort, window, method, or evaluation
  contract; conflicting target, denominator meaning, or source semantics are
  `incompatible`. If any required dimension is unknown, the match is
  `unresolved`, never `exact`; similar wording alone MUST NOT establish
  equivalence. Semantic same-question judgments retain worker/reviewer
  attribution.
- **FR-007**: Before an exact repeat executes, the worker MUST record whether
  valid prior evidence can be used or why a rerun is warranted; an authorized
  replication MUST remain possible.
- **FR-008**: A new window or cohort MUST be represented as related work with
  its changed scope and comparison link, not as an identical cached result.
- **FR-009**: Ledger retrieval MUST respect access scope and record exposure
  when previous outcomes enter the worker context.
- **FR-010**: The ledger index MUST be rebuildable from committed domain
  records and durable rationale events without changing historical results.
- **FR-011**: Redaction or loss of a source after ledger publication MUST
  update current availability and reuse eligibility while retaining the
  permissible historical attempt entry.
- **FR-012**: One analytical attempt is one admission of a comparison purpose
  under a specific Plan revision. It MAY contain several Executions,
  candidate models, Results, and infrastructure retries. A changed
  population, estimand, evaluation contract, or outcome-informed method
  creates a new linked attempt. An authorized replication is also a new
  admission with a distinct purpose instance and linked rationale, even if
  the Plan and analytical identity are unchanged; a shell retry alone is
  not. Redaction eligibility MUST be recomputed against the current source
  grant and availability before reuse.

### Key Entities

- **Ledger entry**: Readable projection of one admitted analysis attempt.
- **Match assessment**: Exact, related, or incompatible prior work with reason.
- **Rerun rationale**: Why prior work was reused, replicated, or superseded.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Every attempt in the fixture has one readable, evidence-linked
  ledger entry after recovery, including the worker-without-prose attempt.
- **SC-002**: Exact repeats are surfaced before execution with all matching
  dimensions; zero new-window or new-cohort fixtures are mislabeled exact.
- **SC-003**: Negative, inconclusive, and failed attempts are retrievable;
  none is presented as a supported answer.
- **SC-004**: A full index rebuild preserves stable attempt IDs, rationale,
  links, access scope, and the distinction between exact and related work.
- **SC-005**: An unknown source version or parameter produces zero exact
  matches, and the reason is visible. A later correction creates a new ledger
  revision while the older view remains inspectable.
- **SC-006**: A redacted-source fixture retains its historical attempt entry
  but yields zero eligible exact-result reuses until evidence is restored or
  recomputed under a valid source version.

- **SC-007**: A restricted-ledger fixture returns zero unauthorized entry
  content and records each admitted retrieval as an exposure event tied to
  the requesting inquiry.
- **SC-008**: A two-model plan with three executions and one infrastructure
  retry has one attempt entry and all four execution events; a changed
  estimand creates a second linked attempt. A revoked source grant prevents
  exact-result reuse while preserving an access-safe historic entry.
- **SC-009**: Frozen exact, new-window related, changed-denominator
  incompatible, and unknown-version unresolved fixtures receive the four
  canonical match states with their compared dimensions and reasons visible.
- **SC-010**: Each fixture ledger entry exposes every FR-002 field, including
  an explicit absence of Results when applicable; a worker-without-prose
  entry preserves controller facts and labels interpretation missing.
- **SC-011**: Failed, null, inconclusive, stopped, and superseded attempts
  remain separately searchable after an index rebuild with their original
  states and linked revisions.
- **SC-012**: Before an exact rerun, the worker records reuse or a deliberate
  replication reason; the authorized replication executes as a linked
  attempt without a false duplicate-block.
- **SC-013**: Two paraphrases of one scoped question match when every
  analytical-identity field is equal; same-named analyses with different
  denominator meaning or source role do not match exactly, and the
  attributed semantic comparison reason is visible.
- **SC-014**: Adding a later outcome, correction, and rerun rationale appends
  durable events to the same attempt history and rebuilds the current
  projection without changing any prior committed event bytes or IDs.
- **SC-015**: On an empty-ledger fixture and on a fixture whose only attempts
  belong to another principal, orientation returns the explicit
  no-prior-attempts outcome with zero entries, zero error states, and zero
  duplicate blocks, and the inquiry proceeds.

## Assumptions

- The ledger is a projection over Question, Plan, Execution, Result, Check
  Result, and Claim records, not a competing mutable result store.
- The first slice warns and records decisions; computation caching or
  automatic suppression can follow only after evidence validity is proven.
- Authorized replication and analyses on fresh data are productive reruns.
- Append-only is an application contract; a privileged local process can
  tamper with files unless an independently protected store is added.

**Proposal trace**: §§4–5, 21.6; T48–T51; I04, I08, I17, I20.
