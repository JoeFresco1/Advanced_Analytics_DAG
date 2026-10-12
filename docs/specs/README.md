# Advanced Analytics DAG specifications

The [product proposal](../docs/specs/advanced-analytics-dag-proposal.md) sets
the vision and system-wide invariants. These twenty-three Spec Kit specifications
make it executable: eight integration milestones prove complete user paths, and
fifteen focused features give the distinct capabilities and failure modes their
own acceptance contracts.

The focused specs are planned and verified within a milestone. Completing a
component alone does not complete its milestone: the worker or computation must
produce it, the controller must consume it, and a user must inspect its effect.
The milestone dependencies below are required ordering constraints; their IDs
remain stable even when new slices are inserted later.

The [FR verification map](../docs/specs/fr-verification-map.md) and
[edge verification map](../docs/specs/edge-verification-map.md) give each
requirement and listed edge case a numbered acceptance check. The
[coverage audit script](../scripts/audit_spec_coverage.py) detects missing or
drifting references. These are specification checks; implementation tests
must still be written and run during the corresponding milestone.

## Integration milestones

| ID | User-visible completion | Depends on | Focused features | Proposal |
| --- | --- | --- | --- | --- |
| [001](001-isolate-analytics-product/spec.md) | Analytics worker context excludes factory semantics while neutral execution remains | None | Repository boundary | §16; T53 |
| [002](002-executable-evidence/spec.md) | Fixed analysis, checks, and evidence trace work through failure and recovery | 001 | 009–011 | Slice A |
| [003](003-delegated-inquiry/spec.md) | Worker answers a question on fixed data within a budget | 002 | 007–008, 012–013 | Slice B; T61 |
| [020](020-find-analytical-answers/spec.md) | Past attempts and answers are recorded and automatically recalled for a related question among 99 files, with evidence and validity visible | 003 | 019, 023 | Slice B2; T56–T57 |
| [004](004-source-memory/spec.md) | A second inquiry uses a validated source skill | 003 | 014–015 | Slice C |
| [022](022-activate-reusable-skills/spec.md) | A learned method or rule is scoped, validated, and used in a later inquiry | 004, 020 | 021; also 014–015 for source skills | Slice C2; T59 |
| [005](005-recurrence-and-use/spec.md) | New windows, corrections, and consequential use stay auditable | 004 | 016–017 | Slice D |
| [006](006-inferential-profile/spec.md) | A planned two-group renewal-rate difference returns a scoped estimate | 003; feature 016 for repeated inspection | Two-group renewal-rate difference specified within 006; no separate component spec | Slice E; T54–T55, T58 |

The B2 and C2 labels are additive slices inserted after the original A–E
sequence; feature and milestone IDs were not renumbered because existing links
and proposal traces rely on them. Feature 006 already specifies the concrete
two-group rate-difference profile; later inferential methods get separate
specs rather than being implied by that row.

Recovery ownership is explicit: focused feature 009 defines atomic evidence
publication and replay after interruption; 019 reconciles the project catalog;
milestone 002 injects failures and verifies the user-visible trace. Checks and
viewer features consume recovered evidence but do not redefine commit state.

Requirement ownership follows the same rule throughout: the focused feature
named in the table owns its domain transition and validation contract. A
milestone FR is an integration obligation that consumes those focused rules;
it cannot rename a state, weaken a failed check, or redefine a dependency
list. The [canonical state vocabulary](../docs/specs/state-vocabularies.md)
owns machine values. If a milestone and focused FR disagree, planning must
resolve the conflict before implementation; neither wording silently wins.

The [access and judgment contract](../docs/specs/access-and-judgment-contract.md)
owns principal/grant inputs and the boundary between controller checks,
worker proposals, and owner decisions. Feature 001 supplies foundational
access enforcement; feature 010 supplies frozen policy registration.

For project memory, 019 owns artifact identity and the rebuildable catalog
index, 023 owns durable attempt entries and exact-match decisions, and 020
owns answer cards, query orientation, ranking, and exposure of retrieved
cards. 020 calls 023 once for prior attempts and searches 019's index; neither
019 nor 023 independently decides which answer card directly answers a new
question. The search index is a projection, not another authoritative record.

## Focused feature specs

| ID | Feature | Depends on | Key proposal cases |
| --- | --- | --- | --- |
| [007](007-data-identity-measurement/spec.md) | Identify data and measurement | 001 | T05 |
| [008](008-plan-exposure-history/spec.md) | Preserve plans and exposure | 001 | T06–T07, T09, T25, T61 |
| [009](009-publish-evidence/spec.md) | Own atomic publication and recovery of exact evidence | 001 | T01–T02, T13–T15, T22, T52 |
| [010](010-run-required-checks/spec.md) | Run required analytical checks | 009 | T02–T04, T10, T23 |
| [011](011-inspect-result-trace/spec.md) | Inspect a result and its claim | 009–010 | T01, T11, T22–T23, T60 |
| [012](012-bound-worker-inquiry/spec.md) | Bound an autonomous inquiry | 008–009 | T04, T12, T26–T27 |
| [013](013-compare-predictive-value/spec.md) | Compare predictive value | 007–008, 010 | T04, T07–T09, T24 |
| [014](014-build-source-skill/spec.md) | Build a source skill | 003 | T17, T25 |
| [015](015-reuse-source-skill/spec.md) | Reuse and refresh a source skill | 014 | T18–T19, T25 |
| [016](016-reconcile-recurring-windows/spec.md) | Reconcile recurring data windows | 003 | T18, T20, T22, T58 |
| [017](017-authorize-claim-use/spec.md) | Review a claim for intended use | 003, 016 | T16, T21, T23 |
| [018](018-evaluate-analytics-product/spec.md) | Evaluate complete analytics work | Fixture registry starts with 001; full scoring follows each completed path | Cases contributed by owning features |
| [019](019-catalog-analytical-artifacts/spec.md) | Catalog outputs, bound exports, manage scratch, and import legacy files | 003 | T28–T30, T37, T39, T43–T47 |
| [021](021-detect-skill-candidates/spec.md) | Propose reusable knowledge from feedback and repetition | 004, 020 | T32–T34, T38, T40 |
| [023](023-record-analysis-ledger/spec.md) | Record each attempt and distinguish exact repeats from related work | 003, 019 | T48–T51 |

## Planning sequence

1. Establish the worker boundary with 001.
2. Deliver evidence end to end: 009, 010, 011, then integrate and test 002.
3. Deliver delegated answers: 007, 008, 012, 013, then integrate and test 003.
4. Deliver result memory: 019 and 023, then integrate and test 020 using a
   crowded folder, a readable attempt timeline, durable inquiry finalization,
   and automatic recall on a later question.
5. Deliver source memory: 014, 015, then integrate and test 004. Capture
   broader reusable methods and rules with 021, then integrate and test 022.
6. Deliver recurrence and use review: 016, 017, then integrate and test 005.
7. Add the inferential profile in 006 once 016's repeated-inspection contract
   exists; 006 does not wait for 005's use-review integration. Apply 018 to
   every milestone and run
   the complete evaluation before a broad reliability claim.

The small three-run cases in individual milestones are deterministic smoke
and regression checks, not release-rate estimates. Feature 018 owns the
frozen evaluation fixtures and reference evaluator from the first slice;
its at-least-20-case gate is only a named fixture-profile gate. Broader
reliability claims require a separate predeclared sample-size and uncertainty
assessment.

The order within a phase can change where dependencies allow. Every spec has a
requirements-quality checklist; its checked boxes attest to the spec's
clarity, not completed software. The twenty proposal invariants remain
binding across all features. `.specify/feature.json` selects the first target,
001, for the next Spec Kit planning command.
