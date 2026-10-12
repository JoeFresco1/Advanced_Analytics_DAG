# Research: Skill Activation and Reuse

No external research or unanswered design questions remain. The frozen local
proposal, feature specifications, state/access contracts, architecture, and
interface registry define the implementation boundary.

## Decisions

### One lifecycle authority, immutable revisions

**Decision**: Use only the canonical `candidate`, `active`, `stale`, and
`revoked` lifecycle states. Record each transition as an append-only project
memory event. Revisions and refreshes create new immutable skill versions;
they never reactivate or rewrite the version used by a prior inquiry.

**Rationale**: This follows the canonical vocabulary, proposal invariants I11,
I16, and I17, and 009's project-memory CAS/replay model. It preserves exact
historical use and makes revocation/staleness visible.

**Alternatives considered**: A separate `pending`, `denied`, or `inactive`
status would add a noncanonical lifecycle state. Keep pending/denied reasons
inside candidate decision records instead.

### Policy-owned independent validation for auto-activation

**Decision**: Automatically activate only project-scoped method candidates
covered by a preapproved low-risk policy, a passing policy-owned validator,
and a clear conflict check. The validator must use an independent fixture or
prior committed execution that the candidate author did not create for that
check. Record exact subject, fixture/execution, policy, validator identity and
version, outcome, and reason. Worker-authored self-tests remain candidate
evidence and cannot satisfy this gate.

**Rationale**: Proposal §21.2 and feature FR-002 distinguish positive
validation from author claims and reserve project-method auto-activation for
preapproved low-risk scope. The independent check supports SC-005 without
changing the frozen evaluator labels or thresholds.

**Alternatives considered**: Trusting a worker-authored test, statistical
significance, model confidence, a successful command exit, or another
unattributed opinion would collapse candidate authorship and independent
validation.

### Independent activation of source candidates

**Decision**: Feature 022 also activates exact committed source candidates
from 014. It independently replays each bounded source recipe under the
candidate's intended current grant and exact source version, validates source
facts, assertion provenance, scope, and conflicts, and records only an
already configured owner or preapproved policy as authority. It consumes
014's source-discovery facade and 015's compatibility predicate instead of
creating new source discovery or a competing matcher. A failed, inconclusive,
candidate-authored-only, ungranted, or unresolved required check keeps the
candidate. Unknown/conflicting business definitions remain explicit and
nonauthoritative; no new policy permission is inferred. The immutable active
version is published through 009 and exposed by the 022 provider before 004.

**Rationale**: The corrected architecture assigns 022 as the production
activation owner for source as well as method/rule candidates and requires
the actual candidate-to-active path before 004's live source-reuse demo.
Feature 014 creates candidates, feature 015 owns source compatibility and
staleness, and 004 consumes rather than simulates 022 activation.

**Alternatives considered**: A seeded active record would not prove
activation; treating a worker-authored recipe check as independent would
collapse authorship and validation; reimplementing 014 discovery or 015
compatibility would create competing ownership; inferring a source policy
from method auto-activation would grant unapproved authority.

### Configured owner governs definitions and high-impact changes

**Decision**: Resolve owner identity from authenticated principal and
project-configured authority records. Owner review is required for a new or
changed authoritative definition and for expanded audience, access, or
release policy. The only non-owner authority is a preapproved policy already
registered for the exact eligible scope; source activation cannot inherit the
method-only auto policy. A recorded, concrete approval by the configured
owner counts once; after independent tests and conflict checks pass, activation
proceeds without another prompt. `skills review` records an approval or
rejection when no qualifying approval is already present.

**Rationale**: The access/judgment contract disallows worker-created grants
and authorities. Proposal case T59 and FR-003 explicitly require one concrete
owner approval to satisfy authority without duplication.

**Alternatives considered**: Treating candidate authorship or an arbitrary
user's assent as authority would silently replace an authoritative rule.
Requiring a second review after a concrete configured-owner approval would
violate the approved single-decision path.

### Structured scope checks; reuse the source predicate

**Decision**: Method and rule versions declare their own deterministic,
versioned scope predicate at activation. All required fields must be known and
match; source, role, population, measure/denominator, and period are explicit
when relevant. Missing scope cannot qualify current guidance. Source-skill
reuse delegates to 015 FR-001's single complete compatibility predicate.

**Rationale**: FR-005 and SC-008 require behavior across source, role, period,
and the declared predicate. Feature 015 owns source compatibility, freshness,
and scoped staleness.

**Alternatives considered**: A keyword or worker-semantic similarity score
would not establish scope compatibility. Reimplementing 015's source checks
would create competing predicates.

### Retrieval and actual use are separate records

**Decision**: Record supplied version/reference IDs during orientation, and
record executed recipe/example IDs only when linked to an actual execution.
Keep prior-result references and exposure separate from general instructions.
Filter current lifecycle, audience, grant, freshness, and scope before context
construction. A skill never expands grants.

**Rationale**: Proposal §21.2, access-and-judgment rules, FR-004 through FR-008,
and the canonical current-validity vocabulary require provenance and exposure
without implying that supplied content was used or understood.

**Alternatives considered**: Marking all supplied references as executed, or
embedding a single study's result as an unscoped instruction, would falsify
usage and erase exposure scope.

### Existing extension and persistence contracts

**Decision**: Implement only the 022 provider and acceptance fixture assigned
in `docs/build/graph.json`. Export `analyticsExtensionV1`; depend on the
`skill-capture` provider; publish records through 009's
  `commitProjectMemory`/`recoverProjectMemory`; use existing 003 command and
  orientation hooks. Keep source discovery in 014 and source
  compatibility/refresh in 015. The corrected graph prerequisites are 003,
  014, 015, 020, and 021; 004 is downstream and consumes the committed active
  source version. Use `pnpm build` and `pnpm test`; add no package dependency.

**Rationale**: `docs/build/interfaces.md` freezes the extension loader,
provider allowlist, project CAS, and lifecycle hooks. `docs/build/ARCHITECTURE.md`
assigns one owner per implementation file and an end-to-end feature path.

**Alternatives considered**: Editing 003 registry/controller, creating a
second storage/index transaction, or changing the package manifest would
violate the shared interface freeze and graph ownership.

## Unresolved questions

None. Policy owner identity and applicable validator profiles are configured
project inputs as specified; this plan does not invent organization-specific
owners or thresholds.
