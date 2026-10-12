# Canonical analytics state vocabularies

**Status:** Normative specification for the analytics domain. The proposal
defines the invariants; focused specs own transition rules; milestone specs
verify their combined user-visible behavior. Machine values below are stable
snake-case identifiers. Prose may use ordinary words only when it does not
introduce another machine state.

## Inquiry outcome

An inquiry is `in_progress` until the controller records exactly one terminal
outcome:

| Machine value | Meaning |
| --- | --- |
| `answered` | The question received an adequately supported scoped answer. |
| `answered_with_uncertainty` | A useful scoped answer was returned with unresolved statistical or methodological uncertainty. |
| `insufficient_evidence` | Available evidence cannot answer the operational question. |
| `needs_input` | A material definition, access grant, or choice requires the user. |
| `budget_exhausted` | The allowance ended before adequate completion. |
| `execution_blocked` | Infrastructure, access, or required computation prevented completion. |

`answered_with_uncertainty` is a valid completed inquiry, not a failed task.
`insufficient_evidence` is not the same as an imprecise measured effect.
Feature 012 owns stopping and transition rules; 003 verifies the full worker
path. A later continuation creates a linked inquiry revision rather than
rewriting the terminal record.

The controller selects the terminal value from recorded evidence, not process
exit status. It first records whether an authorized answer (including an
explicitly uncertain answer) exists. If so, use `answered` when the requested
claim is adequately supported and `answered_with_uncertainty` when the answer
is useful but its stated uncertainty remains unresolved. Otherwise, use
`needs_input` only when a named user decision or grant can unblock the
question; `budget_exhausted` when a declared allowance ended; and
`execution_blocked` when required work failed or access was denied without a
user-resolvable grant. Use `insufficient_evidence` when available, completed
work cannot support an answer and no bounded next step can resolve it. When
several reasons apply, record them all and select the first applicable value
in this order: answer, needs_input, budget_exhausted, execution_blocked,
insufficient_evidence. An answer produced before a later process failure
retains its answer outcome with the failure disclosed. A worker proposes the
semantic answer and reason; the controller checks required fields, evidence
links, policy, and this precedence rule. A challenged semantic judgment
remains attributed to the worker or named reviewer, never to a mechanical
validator.

## Plan exposure and interpretation

Plan interpretation status is exactly `planned_confirmatory`, `exploratory`,
`outcome_informed`, or `exposure_unknown`. Feature 008 owns transitions.
`planned_confirmatory` requires a locked plan and declared inspection rule
before relevant outcome exposure. Unplanned analysis is `exploratory`.
Observation of relevant outcomes before a substantive plan revision makes
the revised analysis `outcome_informed`; no approval, worker replacement, or
new execution restores its earlier label. Missing exposure history is
`exposure_unknown` and cannot be presented as planned confirmation. A fresh
independent data boundary may support a new linked confirmatory plan if its
actual access guarantee is recorded.

Feature 016 owns the shared repeated-inspection contract. A plan records the
inspection schedule, comparison family, error-control or sequential method,
and stopping rule before the first relevant look. Features 006 and 013 bind
their inferential or predictive parameters to that contract. An undeclared
look or post-result comparison choice records exposure and changes the
interpretation to `outcome_informed`; it never becomes an infrastructure
retry. A method's practical margin is its predeclared gain threshold in the
metric's units; feature 013 calls that threshold `delta`.

## Required check and supported presentation

Check outcome is exactly `pass`, `fail`, `inconclusive`, `not_applicable`, or
`error`. `missing` and `stale` describe required-check **coverage**, not a
validator's computed outcome. A `not_applicable` result satisfies a required
condition only when the frozen policy explicitly permits it and its reason is
recorded. Otherwise its coverage is invalid.

For each dependent Claim, feature 010 computes
`supported_presentation_eligible: true | false` against the frozen policy and
the exact claim, result, check, artifact, validator, and policy revisions.

| Required-condition state | Supported presentation |
| --- | --- |
| Current `pass` | This condition is satisfied. |
| Current `not_applicable` with policy-supported reason | This condition is satisfied as not applicable; it is never relabeled pass. |
| `fail`, `error`, or `inconclusive` | Blocked for the dependent claim. |
| `missing`, `stale`, or `not_applicable` without a policy-supported reason | Blocked for the dependent claim. |

One blocked required condition makes
`supported_presentation_eligible = false`. The result and check remain in
history. The system may still report an honest failure, negative finding, or
inconclusive answer with the limitation visible. Human risk acceptance affects
authorization for a use; it cannot turn the failed condition into a pass or
retroactively make the claim supported. Feature 010 owns this rule; 002 tests
it across computation, checks, and viewer.

## Claim evidence state

Claim evidence state is one of `supported`, `unsupported`, `inconclusive`, or
`conflicting`. It describes the interpretation under the current evidence
revision, not process success or permission to use it. `supported` requires
supported-presentation eligibility and adequate method-specific evidence.
`unsupported` means the proposed assertion lacks valid support, including a
failed required integrity condition. `inconclusive` means usable evidence does
not resolve the question. `conflicting` means materially comparable results
point in opposing directions. A new evidence revision yields a new assessment;
the old state remains historically inspectable.

`blocked` is a controller action on supported presentation, not a claim
evidence state. `restricted` and `qualified` describe report wording or use
limits, not additional state values. Viewer text must show the actual evidence
state and the blocking or qualifying reason. Intended-use authorization is a
separate assessment.

Feature 017 owns a `conflicting` reassessment when two methodologically
comparable, current Results support materially opposing answers to the same
operational Claim. It records the compared Result IDs, the conflict criterion,
and the reviewer or worker attribution. Merely different populations or
estimands are not a conflict; they require separate scoped claims.

Predictive comparison classification is a separate dimension: `improvement`,
`meaningful_improvement_excluded`, or `inconclusive`. Feature 013 applies these
only after required validity checks pass. This `inconclusive` value is
qualified by the `predictive_comparison` field and is not the inquiry outcome,
Claim evidence state, or a Check outcome.

## Current validity and period comparison

Current validity is exactly `current`, `reassessment_required`, or
`superseded`. Any revision of a bound source, artifact, method, policy,
scope, or definition sets dependent current records to
`reassessment_required` until a new assessment resolves them. A newer
record that explicitly replaces an earlier answer sets the earlier answer
to `superseded`, without deleting it. Availability and reproduction are
separate dimensions; lost bytes are not silently called invalid evidence.
Feature 016 owns correction traversal, 019 detects imported-file changes,
and 020 displays current validity.

Period comparison state is exactly `unassessed`, `comparable`,
`qualified_comparison`, or `incomparable`. Feature 016 compares definition,
population, window coverage, weighting, and freshness. All match and complete
means `comparable`; a documented difference with a predeclared adjustment
and bounded interpretation means `qualified_comparison`; an unresolved
definition, population, or coverage mismatch means `incomparable`. Missing
required metadata remains `unassessed` and cannot produce an unqualified
trend. The assessment records exact compared window and definition versions.

Ledger match state is exactly `exact`, `related`, `incompatible`, or
`unresolved`. Feature 023 owns it. `exact` requires every analytical-identity
dimension known and equal. `related` shares the operational question but
has a known changed cohort, window, method, or evaluation contract.
`incompatible` has a known conflicting target, denominator meaning, or
source semantics that prevent the prior answer from addressing the new
question. An unknown required dimension is `unresolved` regardless of text
similarity. A worker supplies any semantic same-question judgment with a
reason; the controller checks the dimension comparison and provenance.

Per-card retrieval classification is exactly `direct_answer`, `related`, or
`not_relevant`. Feature 020 owns it. A `direct_answer` requires known,
compatible source, metric, population, period, and question intent with
current evidence. A known mismatch may be `related` only with its reason
shown; missing required scope makes a card `related` at most. A card with no
substantive question connection is `not_relevant`. Query-level search outcome
is `direct_answer_found` or `no_match`; `no_match` means no accessible card
qualifies as a direct answer. These values do not change Claim evidence state.

## Artifact roles and retention

Feature 019 owns artifact role values `input_extract`,
`intermediate_table`, `plot`, `model`, `diagnostic`, `result_table`,
`report`, `other`, and `unknown_imported`. The role describes intended use,
not evidence quality. A worker suggestion cannot silently change it after
commit. Retention class is `scratch`, `cache`, `evidence`, or `deliverable`.
Only managed `scratch` and eligible `cache` may be automatically cleaned;
`evidence` and `deliverable` require their declared retention policy and
dependency check. Availability is independently `available`, `unavailable`,
or `redacted`, with a reason and effective time. A role or class change makes
a new version rather than rewriting the original classification.

## Reproduction and availability

Reproduction state is exactly `not_attempted`, `reproduced`, `mismatched`, or
`unavailable`. `reproduced` records the declared comparison rule (exact bytes,
canonical table, or numerical tolerance) and its observed agreement.
`unavailable` means the required inputs or artifacts cannot currently be
accessed; it is not a successful reproduction. Evidence availability is a
separate property, so a historically reproduced result can later have an
unavailable source. Feature 009 owns the comparison contract; 005/016 consume
current validity after corrections and retention changes.

## Unknown metadata

`unknown` is the explicit sentinel for an unavailable source, grain,
cardinality, definition, or version fact. It is a field value, not a positive
check or an assertion that the fact does not exist. A later observed or
declared value creates a new assessment; unknown metadata cannot silently
qualify an exact match or a supported transformation.

## Skill lifecycle

Each immutable skill version has one current lifecycle state:

| State | Meaning |
| --- | --- |
| `candidate` | Proposed or under validation/review; not supplied as active guidance. |
| `active` | Validated and authorized for the recorded scope. |
| `stale` | A relevant assumption or source fact changed; current use requires refresh or explicit historical framing. |
| `revoked` | Authority withdrew the version; it cannot guide new work. |

There is no `inactive` state. A proposal awaiting authority remains
`candidate`, with its pending reason. Refresh or correction creates a new
version; it never turns an old run's pinned version into another version.
Features 014/021 create candidates, 022 owns activation, and 015 owns reuse
and stale-version handling.

## Intended-use authorization

Authorization is assessed for an exact requested use and evidence revision.
Its machine state is `not_assessed`, `pending_review`, `authorized`,
`risk_accepted`, `denied`, `stale`, or `revoked`. Feature 017 owns the
transitions. `authorized` requires the configured policy and authority;
`risk_accepted` additionally names the accepted uncertainty and accountable
actor. A review request with no substantive rationale remains
`pending_review`; only an explicit negative authority decision is `denied`.
Merely opening the trace cannot approve it. A material evidence, scope, or
policy change makes the current use assessment `stale`. Withdrawal of
authority makes it `revoked`. Reassessment
creates a new linked assessment; it never edits the old decision. A Decision
record points to the use assessment and exact Claim revision, but does not
change Claim evidence state.

For this transition, material means any changed bound Result, Check Result,
source or artifact revision, Claim interpretation, intended audience/use,
authority, or frozen policy that could affect eligibility or meaning. The
controller marks the dependent use assessment `stale` on that revision; a
reviewer may later decide that the change does not alter the answer, but may
not suppress the staleness event. `not_assessed` is the initial state before
an intended use is requested. A complete review with an explicit negative
decision is `denied`; a missing rationale or unresolved required review
remains `pending_review`.

## Cross-state rule

These dimensions are independent. A computation may finish, a required check
may fail, its result may remain valid as an observation, a Claim may be
`unsupported`, and the inquiry may still end as `answered_with_uncertainty`
when it honestly reports that limitation. Reports, tests, and catalogs must
not collapse those facts into one green or red status.
