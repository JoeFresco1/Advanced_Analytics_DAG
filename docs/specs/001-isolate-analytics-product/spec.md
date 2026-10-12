# Feature Specification: Isolate the Analytics Product

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Turn the dedicated analytics fork and the product proposal's repository-boundary decision into a worker environment that consistently treats a task as analytics.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Start an analytics worker (Priority: P1)

An analyst delegates a data question. The worker sees analytics instructions,
examples, and terminology and can describe its job without importing software
factory concepts.

**Why this priority**: The user's reason for the fork is to prevent the worker
from mistaking analytical work for software delivery.

**Independent Test**: Start a worker in a clean checkout with a neutral data
question and inspect the supplied context and first proposed actions.

**Acceptance Scenarios**:

1. **Given** a fresh analytics checkout, **when** a worker receives a data question,
   **then** its default instructions and examples describe analytical questions,
   data, results, and evidence.
2. **Given** the same checkout, **when** the worker proposes an analysis,
   **then** its first proposed actions contain no pull-request, defect-hunt,
   code-review, or integration-branch step. A question that genuinely requests
   software-delivery work is recorded as an out-of-scope request under the
   stated assumption; it is never reclassified into an analytics plan.

### User Story 2 - Reuse reliable execution (Priority: P2)

A maintainer can run and recover task graphs in the analytics product without
maintaining a second, independently modified scheduler.

**Why this priority**: Analytics needs the existing runner's reliability while
owning its evidence and review semantics.

**Independent Test**: Run a neutral task graph containing dependencies, a failed
process, and a recovery; compare observed execution behavior to the supported
engine contract.

**Acceptance Scenarios**:

1. **Given** a neutral task graph, **when** its tasks execute, **then** dependency
   order, logs, timeouts, and recovery remain available in the analytics product.
2. **Given** a failed analytical check, **when** the product interprets the result,
   **then** software review or repair rules do not decide the analytical claim.

### Edge Cases

- A copied upstream document still contains software-factory examples.
- A generic runner event uses the word “success” while analytical evidence fails.
- A worker can search the repository broadly and encounter transitional modules.
- An engine update changes a shared execution contract used by both products.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The default worker context MUST identify this repository as the
  advanced analytics product and point to its product contract.
- **FR-002**: Default worker instructions, examples, and discovery paths MUST
  exclude software-factory prompts, fixtures, terminology, and tasks.
- **FR-003**: At completion of this boundary milestone, transitional
  software-factory prompts, fixtures, and task templates MUST be absent from
  the analytics worker's checkout and repository search paths. A context-only
  filter is an explicitly incomplete migration state under FR-008.
- **FR-004**: The analytics product MUST retain a neutral execution facility for
  dependencies, processes, logs, bounded work, locking, artifacts, and recovery.
- **FR-005**: Analytics-specific evidence and review outcomes MUST remain under
  analytics authority; a neutral process status MUST NOT imply claim support.
- **FR-006**: Shared engine changes MUST have one authoritative source and an
  explicit compatibility path for both products.
- **FR-007**: The product MUST state which inherited behaviors are still active
  during migration and MUST NOT present unimplemented analytics features as live.
- **FR-008**: A context-only boundary MUST be labeled as such; the product
  MUST NOT describe factory files as physically inaccessible until the
  worker's actual checkout and search permissions prove that guarantee.
- **FR-009**: Boundary completion MUST include a live worker question, a
  controller-run neutral task, and a user-inspectable context and outcome;
  repository edits alone MUST NOT count as a finished product slice.
- **FR-010**: The analytics adapter MUST disable generic task retries,
  review-rejection redo, repair rounds, and harness fallback for analytical
  tasks by default. It MAY admit a new infrastructure attempt only after
  classification and MUST record that attempt separately. A failed analytical
  check or valid negative result MUST NOT invoke software repair behavior.
- **FR-011**: The product MUST bind each worker, source-adapter, catalog, and
  viewer request to a configured principal and grant under the normative
  access contract. Missing grants deny access. Where a shell can bypass this
  product boundary, the product MUST label the guarantee as workflow-level.

### Key Entities

- **Worker context**: The instructions, references, examples, and allowed source
  tree supplied to an analytics worker.
- **Execution contract**: The neutral behavior available to both products.
- **Domain outcome**: The analytics interpretation of an execution and its
  evidence, distinct from process completion.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: In a clean worker-context inspection, zero software-factory files
  or instructions are included in the default analytics context.
- **SC-002**: All tested neutral execution behaviors—dependency order, logs,
  timeout, and crash recovery—remain available after isolation.
- **SC-003**: In three recorded worker starts with different neutral data
  questions, zero initial responses classify the task as software delivery.
- **SC-004**: The failed-check demonstration retains the failure as analytical
  evidence and triggers zero generic retries, review redos, repair rounds, or
  fallback invocations, counted the same way as SC-007.
- **SC-005**: The fixture neutral engine behavior has one authoritative source
  and a recorded compatibility check against both products after a shared
  contract change.
- **SC-006**: The transitional-behavior inventory names every inherited
  behavior active in the fixture; zero unimplemented analytics features are
  described as live.
- **SC-007**: In a clean checkout, repository search finds zero reachable
  factory prompts, fixtures, or task templates; a deliberately failed check
  and a valid negative result trigger zero generic retries, review redos,
  repair rounds, or fallback invocations.
- **SC-008**: In a two-principal fixture, the controller, source adapter,
  catalog, and viewer deny every ungranted source and sheet request while
  allowing the explicitly granted operations; the displayed enforcement
  label matches whether the worker shell itself is isolated.
- **SC-009**: One live neutral data question runs through the controller and
  opens a user-inspectable context and outcome in the analytics product;
  repository file changes without that demonstration do not satisfy 001.
- **SC-010**: During migration, a context-only filter is labeled as such and
  makes zero claims that searchable factory files are physically inaccessible;
  after boundary completion the checkout search in SC-007 is the evidence for
  the stronger claim.

## Assumptions

- This feature is a repository and context boundary; it does not implement the
  analytics evidence workflow specified in feature 002.
- The existing orchestrator remains the source of neutral execution behavior
  until a shared engine boundary is extracted.
- A worker expressly asked to analyze software development data may still use
  software terms when relevant to that data question.

**Proposal trace**: §§2–3 and 16; T53; invariants I04, I13–I14.
