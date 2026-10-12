# Feature Specification: Catalog and Manage Analytical Artifacts

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Register committed analytical outputs, bound and clean managed scratch work, and inventory legacy CSV and workbook files without fabricating their origin or importance.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Know what a run produced (Priority: P1)

An analyst can inspect every committed output from a run and see whether it
was an input, intermediate table, diagnostic, result, plot, or report, and
which claim or answer used it.

**Why this priority**: A folder of files cannot tell the analyst which output
was evidence and which was a discarded exploration.

**Independent Test**: Run a fixed inquiry producing both used and unused
outputs, then inspect the inventory and its links.

**Acceptance Scenarios**:

1. **Given** a completed inquiry, **when** its files are opened, **then**
   every committed output has stable identity, producer, role, availability,
   and links to the results that use it.
2. **Given** an uncommitted staged output, **when** the inventory refreshes,
   **then** it is not promoted as a completed result.

### User Story 2 - Triage a crowded legacy folder (Priority: P1)

An analyst selects a directory with at least 99 CSV and workbook files. The
system inventories them, groups duplicate content, and shows what is known
and unknown without moving the originals or inventing a result story.

**Why this priority**: Existing analytical work must become findable even
when past agents left no manifest or report.

**Independent Test**: Import a fixture of 99 mixed files with duplicates,
missing origins, and changed files.

**Acceptance Scenarios**:

1. **Given** the legacy folder, **when** import finishes, **then** each
   readable file has its own record, duplicate bytes are grouped, and
   uncertain provenance is labeled unknown.
2. **Given** a changed or missing imported file, **when** rechecked, **then**
   its availability or version changes without silently reusing the old one.
3. **Given** a workbook with sensitive rows and a restricted audience,
   **when** an unauthorized worker searches the catalog, **then** neither
   row text nor restricted title appears in results or model context.

### User Story 3 - Finish without a spreadsheet pile (Priority: P1)

An exploratory worker may compute many temporary tables, but the analyst gets
one answer and only the requested, evidence-linked deliverables. The worker
does not export a CSV or workbook for every step.

**Why this priority**: A searchable catalog alone would still leave the user
with a huge and costly folder of files.

**Independent Test**: Run a fixture that creates 1,000 scratch tables and
requests 1,000 workbook exports, then request one legitimate workbook.

**Acceptance Scenarios**:

1. **Given** the default profile, **when** the worker requests 1,000 workbook
   exports, **then** none are published and the count violation is visible;
   the structured results and answer remain available.
2. **Given** a user requests one workbook, **when** it is exported, **then**
   it comes from a pinned Result version and links back to the answer card.
3. **Given** unused managed scratch files, **when** retention cleanup runs,
   **then** only eligible scratch is removed and the result trace still works.

### User Story 4 - Choose a notebook when it helps (Priority: P2)

When the optional notebook-delivery profile is enabled, an analyst can
request an editable Jupyter notebook. It opens with a clean, repeatable
computation and shows which committed results its displayed numbers represent.

**Why this priority**: An interactive notebook is useful for inspection and
extension, but hidden state must not become the only copy of an answer.

**Independent Test**: Request a notebook, rerun it from a fresh state, then
try a notebook whose output depends on an earlier unrecorded cell execution.

**Acceptance Scenarios**:

1. **Given** a reproducible notebook, **when** it is published, **then** its
   input versions, environment, execution order, and Result references are
   recorded.
2. **Given** hidden-state output or a failed clean rerun, **when** publication
   is attempted, **then** the notebook remains labeled exploratory and does
   not support an authoritative Claim.

### Edge Cases

- Two paths contain identical bytes but represent separate user-visible files.
- An Excel workbook has several sheets; parsing some sheets is not permitted.
- A filename says “final” but there is no evidence it was used.
- A catalog update fails after evidence was committed.
- A file contains sensitive values that must not enter a search index.
- A local harness can write outside declared paths despite output contracts.
- Cleanup is interrupted after deleting some eligible managed scratch files.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each committed output MUST have a stable catalog identity,
  digest, media type, role, producing record, availability, access scope,
  and links to any consuming result, claim, or report.
- **FR-002**: The catalog MUST distinguish committed evidence from staged,
  failed, and imported material.
- **FR-003**: A bounded importer MUST inventory selected CSV and workbook
  files without moving them by default and record limited permitted metadata.
  The initial profile admits at most 100 files, 1 GiB total input bytes, and
  300 seconds per import; a larger declared owner-approved profile creates
  a new bounded run rather than silently relaxing the limit.
- **FR-004**: Imported files with no traceable execution MUST retain an
  explicit unknown-origin state.
- **FR-005**: Identical bytes MUST be grouped as duplicates while every
  original path remains identifiable. Group membership asserts byte equality
  only: it MUST NOT imply redundancy, supersession, or deletability, MUST NOT
  merge groups across access scopes, and MUST NOT by itself authorize any
  cleanup action.
- **FR-006**: Changed or missing imported files MUST receive a visible
  version or availability change. A changed file marks dependent current
  records `reassessment_required`; a missing file records `unavailable`
  availability without rewriting historical provenance.
- **FR-007**: Sensitive row content MUST NOT be copied into searchable
  summaries by default; search results MUST respect source access scope.
- **FR-008**: The catalog MUST reconcile committed evidence after a crash
  without duplicating records or promoting partial output.
- **FR-009**: Output admission MUST enforce declared roles, count limits, byte
  limits, and retention classes for controller-published artifacts.
- **FR-010**: The default profile MUST publish zero CSV and workbook exports;
  user requests or project deliverable policy MAY authorize bounded exports.
- **FR-011**: A deliverable export MUST bind to a committed Result version and
  answer card; the Result MUST remain accessible independently of the export.
- **FR-012**: Retention cleanup MUST affect only eligible files under managed
  roots, preserve required evidence and durable records, and record deletions;
  imported and user-owned files MUST NOT be routinely deleted.
- **FR-013**: A published notebook MUST pass a clean rerun against pinned
  inputs, record its environment and execution order, and bind displayed
  claims to Result IDs; otherwise it MUST be labeled exploratory.
- **FR-014**: The product MUST distinguish enforced write-root restrictions
  from declared paths in an unrestricted local harness; unmanaged files MUST
  be surfaced and the controller MUST NOT claim it prevented their creation.
- **FR-015**: Workbook import MUST honor sheet-level access; an unreadable or
  unauthorized sheet MUST retain metadata only when the current grant permits
  sheet metadata. Otherwise it MUST retain only an access-safe unavailable
  pointer. Neither case may leak cell content into profiles or search.
- **FR-016**: Managed scratch cleanup MUST be idempotent after interruption;
  recovery MUST reconcile the deletion log and remaining files without
  touching committed evidence or user-owned paths.
- **FR-017**: The catalog index MUST be rebuildable from committed manifests,
  import records, and durable curation events with stable artifact IDs,
  access scopes, availability, and usage links. Index loss MUST NOT make
  uncommitted output authoritative.

### Key Entities

- **Artifact record**: File identity, role, origin, integrity, and availability.
- **Import record**: Selected legacy path and what was actually observed.
- **Usage link**: A result, claim, or report that consumed an artifact.
- **Retention class**: Scratch, evidence, cache, or deliverable with an
  explicit availability and cleanup rule.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: All readable files in the 99-file fixture are inventoried;
  zero receive invented execution provenance or a story from their filename.
- **SC-002**: Duplicate files are grouped without losing any original path.
- **SC-003**: After an interrupted catalog update, all committed fixture
  outputs are indexed exactly once and no staged output appears as a result.
- **SC-004**: The 1,000-table fixture publishes zero default workbook or CSV
  exports; the one requested workbook resolves to its committed Result.
- **SC-005**: Cleanup removes zero files outside managed scratch and preserves
  every evidence link used by the fixture answer.
- **SC-006**: When notebook delivery is enabled, the clean notebook fixture
  publishes with Result links; the hidden-state fixture is labeled exploratory
  and supports zero Claims.
- **SC-007**: An unrestricted-worker fixture that writes outside the managed
  root produces a visible unmanaged-output warning and no enforcement claim.
- **SC-008**: Restricted-row and restricted-title fixtures leak zero values to
  unauthorized catalog search or worker context.
- **SC-009**: A mixed-permission workbook imports only authorized sheets;
  unauthorized cells appear zero times in profiles and search.
- **SC-010**: An interrupted cleanup can be resumed twice with identical
  final managed-file inventory and zero evidence or user-file deletion.

- **SC-011**: Changing one imported file and removing another marks both
  catalog records visibly unavailable or changed on rescan while preserving
  their prior identities and historical metadata.
- **SC-012**: Deleting only the rebuildable index and rebuilding it preserves
  every committed fixture artifact ID, scope, availability, and usage link;
  staged outputs remain absent.
- **SC-013**: A 99-file import completes under the initial bound; the 101-file
  fixture stops at the declared limit with zero unrecorded over-limit reads.
- **SC-014**: Every committed fixture artifact exposes its stable ID, digest,
  media type, canonical role and retention class, producer, availability,
  scope, and Result/Claim/Report links. Staged, failed, and imported files
  retain distinct origin states and none gains invented provenance.
- **SC-015**: An undeclared role, 101st controller-published artifact under a
  100-output fixture limit, missing retention class, or byte-limit overflow
  is rejected before
  publication with committed evidence intact; metadata-only workbook access
  reveals no cells, and a grant without metadata access reveals no sheet name.
- **SC-016**: Import fixtures crossing the initial 1 GiB or 300-second bound
  stop at the recorded limit without moving originals or inventing records
  for uninspected files; a larger import uses a separately authorized
  versioned bounded profile.
- **SC-017**: A publishable notebook fixture records pinned inputs,
  environment, execution order, clean-rerun result, and Result IDs for every
  displayed Claim; a notebook missing any one remains exploratory.

## Assumptions

- The first import is project-scoped and read-only toward existing files.
- A file's role can be declared by its producing task; imported roles may
  remain unknown or suggested until supported by evidence.
- Large file contents remain in approved artifact locations, not in the
  searchable metadata catalog.
- Notebook creation is optional; the default computation is a versioned
  program or controlled request plus structured Result records.

**Proposal trace**: §§11, 21; T28–T30, T37, T39, T43–T47; I15, I18–I19.
