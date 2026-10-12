# 18-bootstrap plan — Frozen synthetic cases and evaluator foundation

## Scope

Plan the original `18-bootstrap` graph node, which depends on graph node `001`.
This is a non-feature build node: it establishes the frozen synthetic fixture
package, metric registry inputs, fixture lock, and base independent-evaluator
interface consumed by feature 018 and feature acceptance suites. It does not
implement feature 018's whole-product scoring or release decision, and it does
not take primary ownership of any proposal case assigned to another feature.

The implementation owns exactly the graph outputs:

- `src/analytics/evaluation/registry.ts`
- Exact base-package files listed in `tasks.md` under
  `src/analytics/evaluation/fixtures/`
- `src/analytics/acceptance/18-bootstrap.test.ts`
- `docs/build/evaluation-fixture-lock.json`

The task manifest lists each base fixture file explicitly. It does not assign
the complete fixtures directory or a contribution subtree to this node.
Feature owners keep later cases and their descriptors in feature-scoped files
and submit those files to the registry by content digest.

Accepted specifications, quality checklists, thresholds, reference answers,
and domain-case ownership are read-only. The 018 bootstrap checks prove the
fixture and evaluator boundary; they are not a final release assessment or a
general reliability claim.

## Contract inputs and semantic boundaries

Use feature 018 and its quality checklist, the product proposal and T01–T61
acceptance matrix, the constitution, canonical state vocabularies, access and
judgment contract, verification maps, build architecture/interfaces, and the
graph-owned `18-bootstrap` instructions. The proposal cases remain analytical
acceptance cases, not implementation tasks or software repair states.

Preserve the product invariants in the package and tests:

- A process exit, task count, or successful API call is not analytical support.
  Correct, negative, null, inconclusive, conflicting, failed, and incomplete
  outcomes remain representable.
- Case inputs and source grants are distinct from reference answers, relevance
  labels, fault descriptions, and adjudication decisions. The worker-context
  view contains only the permitted synthetic inputs and applicable policy.
  Reference adjudication is a separate post-response operation. Tests exercise
  direct context, retrieved answer, source-skill, and cached-summary exposure.
- A local worker may be able to read the checkout. Label this fixture profile
  `workflow-withheld; access isolation not enforced`; do not claim protected
  holdout isolation.
- Deterministic generation uses a pinned generator version, seed, canonical
  serialization, and content digest. IDs and digests are stable across runs.
  Changing an existing expected answer or label requires a new fixture version
  before evaluation; observed product output cannot modify a frozen profile.
- Feature owners may append their scoped cases and evidence before evaluating
  their implementation. A contribution creates a new immutable child lock
  revision and cannot replace existing cases, labels, rubrics, or thresholds.
  `proposalCaseRefs` retain the primary T01–T61 owner assigned by the
  acceptance plan.
- Every case has a frozen `development` or `evaluation` role in the fixture-set
  version and digest. A development case used for tuning cannot be relabeled
  as evaluation in that version; recording its exposure cannot be cleared by
  copying or editing its role. Workflow withholding is not filesystem
  isolation and does not make an exposed case held out.
- The base registry accepts later feature execution evidence without allowing
  evidence registration to mutate the metric registry or any locked fixture
  revision. A contributed case is admitted only to a new child lock revision;
  the parent lock and its expected outcomes/thresholds remain unchanged.
- Initial metric declarations are frozen in this node. A batch cannot be
  admitted until feature 010's configured project owner supplies a matching
  approval reference and timestamp strictly earlier than the batch start. The
  bootstrap does not invent that actor or timestamp.

## Fixture package

Version the package and each fixture profile independently. Use synthetic
values and local generated files only. Keep worker-facing fixture definitions
separate from evaluator-only references in both types and loaders; a
`WorkerFixtureV1` must not contain reference answer, relevance-label, or
planted-fault fields. The evaluator API resolves references only after a
worker response is complete.

The base contract uses distinct versioned records for `WorkerFixtureV1`,
`EvaluatorReferenceV1`, `EvaluationMetricV1`, `AdjudicationV1`, and
`FeatureExecutionEvidenceV1`. The evaluator-only fixture manifest pins each
case's `caseRole` to exactly `development` or `evaluation`; this role is
excluded from worker payloads and covered by the canonical fixture and
fixture-set digests. Freeze at least 20 evaluation-role question cases for the
initial question denominator, keeping development cases outside scored
cohorts. Record tuning/exposure use against the stable case reference so an
exposed development case cannot be promoted within the same fixture-set
version. Derive fixture IDs from the pinned profile,
generator version, seed, and stable case key. Compute SHA-256 over canonical
UTF-8 JSON with stable key ordering; exclude generation timestamps from
fixture identity. The worker fixture carries only question, permitted synthetic
source refs, applicable grants/policies, and allowed task context. The
evaluator reference carries expected answer limits, labels, fault semantics,
and rubric. A post-response adjudication binds the worker response digest to
the exact fixture, reference, rubric, and evaluator versions. Deterministic
checks judge exact values, units, denominators, and state limits; a semantic
judgment that cannot be mechanically resolved retains its named independent
adjudicator and rationale rather than being attributed to the worker.
Execution evidence records the question/case refs, worker/tool/model and
parameters, harness/version, policy, enforceable and observed resource limits,
source/prompt/context digests, fixture and rubric digests, and returned
artifact/evidence refs needed to reproduce the configuration. Comparative
validation rejects runs with different task constraints, harness/version,
budget, fixture IDs, or adjudication protocol; a valid matched comparison
retains identical fixture IDs and scoring-rubric references. Optional
review-disagreement records preserve the sampling frame, policy version, risk
strata, selection rule, adjudicator, disagreement severity, counts,
uncertainty, and a precision target declared before sampling. A record below
its predeclared precision requirement is descriptive only. Adjudication is
append-only and cannot automatically change a policy or metric threshold; the
record must keep the policy/threshold digest unchanged across the audit.

The first frozen package includes:

1. **Question/evidence cases.** At least 20 deterministic evaluation-role
   question cases cover valid, negative/null/inconclusive, and planted-fault
   variants. Development cases are separate and do not enter scored cohorts.
   The package records the question, exact synthetic source/version, grant profile,
   generation rule, valid answer limits, planted-fault definition, and
   independent adjudication rubric. Seed at least 20 planted faults, 20 valid
   claims, 20 displayable numerical assertions, and 20 admitted-attempt
   references where the corresponding 018 gate requires those denominators.
   Include binding, denominator/unit, omitted-check, leakage/exposure, and
   unavailable-evidence faults. Keep these distinct from valid negative or
   null findings; a failed check remains evidence and cannot initiate a
   favorable-result retry.
2. **Retrieval.** Freeze exactly 150 scoped answer-card fixtures and 20
   annotated relevance queries, including the primary relevant card and
   keyword/cohort/period distractors for each query. Freeze five additional
   no-match queries, one of which uses an empty corpus. Labels and scope
   mismatches are evaluator-only. Preserve 020's Hit@3 denominator of 20 and
   the zero-false-direct-answer outcome for the five no-match queries.
3. **Legacy import.** Generate a deterministic corpus of 99 mixed CSV and
   workbook files with duplicate bytes at distinct paths, missing provenance,
   and varied readable metadata. The generator manifest pins file identities,
   formats, sizes, source versions, and digests. The expected limits prohibit
   fabricated execution provenance or a story inferred from a filename.
4. **Skill capture.** Freeze 48 adjudicated signals: eight positive examples
   for each of method endorsement, repeated validated work, costly discovery,
   and validated correction; eight negative answer-only praise examples; and
   eight negative unvalidated-significance examples. The positive denominator
   is exactly 32. Duplicate proposals count as incorrect proposals.
5. **Profiles.** Define synthetic source identity/version and source/grant
   profiles with principal or role, source, operation, audience, and effective
   period. Include permitted aggregate access, restricted row/sheet access,
   and denied/revoked or expired access so disclosure tests have explicit
   inputs. The worker cannot author grants. Reference-key access is not a
   worker source grant.

The expected-answer rubric defines accepted outcomes and scope, numeric
comparison rules, denominator/unit requirements, permitted uncertainty, and
claims that must remain unsupported when evidence is absent or faulted. Each
case records permitted terminal/evidence interpretations, supported claim
scope, forbidden extrapolations, exact or predeclared-tolerance numeric
targets, expected denominator and units, and acceptable honest non-answer
states. Tolerances are set by the generator/rubric before any worker response;
they are never widened after observing output. The rubric must not require
favorable outcomes. Adjudication records the rubric version, adjudicator
identity/type, evidence references, disposition, and rationale.
Review-disagreement sampling is descriptive at small sample sizes and cannot
automatically change policy thresholds.

Proposal cases T01–T61 remain feature-owned. The registry records their stable
case references and owner evidence as they arrive, spanning evidence and
exposure cases (T01–T27, T52–T55, T58, T60–T61), catalog/retrieval/ledger cases
(T28–T31, T35–T37, T39–T42, T48–T51, T56–T57), skill cases (T17–T19, T25,
T32–T34, T38–T40, T59), and output-management cases (T43–T47). These groups
are navigational references, not new ownership assignments.

## Feature-scoped contributions and batch selection

The accepted architecture lets features add cases without transferring their
primary T01–T61 acceptance ownership to 18-bootstrap. The registry accepts a
`FeatureFixtureContributionV1` descriptor from a configured feature owner.
Each descriptor binds the contributing feature and owner reference, fixture
IDs, the supplied worker-fixture content digests, a parent lock revision,
independently adjudicated expected outcomes and rubric digests, and an
approval record/time. The approval record is owner-authored, immutable, and
bound to the canonical contribution and expected-outcome digest. Expected
outcomes are fixed before contributed cases can
be selected for evaluation; they cannot be authored or revised from the
implementation's observed output. Owner authorization is separate from
feature 010's configured project-owner approval of the metric registry.

Submission validates that every fixture ID is new and every content digest
matches the supplied feature-owned bytes. A repeat is idempotent only when
the owner, parent revision, fixture digests, expected outcome/rubric digests,
and approval binding all match. Reusing an ID with changed content is a
collision. Missing, unapproved, or unauthorized contributions are rejected.
Both approval time and registry-admission time must be strictly earlier than
the target batch start. The registry stamps admission from its configured
clock; a contributor cannot choose that timestamp. A batch already started cannot receive more cases. An
existing revision can still parent a child revision for a future batch; the
child never changes what an earlier batch selected.

The registry returns a new immutable child revision with a stable ID and
digest. Its parent reference, existing fixture IDs, reference outcomes,
rubrics, metric-registry digest, and thresholds remain unchanged; only the
new feature-scoped contribution is appended. Batch start pins the exact
revision reference. The evaluator selects fixtures and references from that
revision, not the current registry head, so later contributions cannot enter
an already-started or completed batch. The static
`docs/build/evaluation-fixture-lock.json` describes the initial revision and
versioning contract; subsequent child revisions are returned as immutable
registry records for the owning evaluation store to persist.

Each contributor declares its own exact contribution file in its feature
manifest, for example
`src/analytics/evaluation/fixtures/contributions/020/retrieval.json`. No
18-bootstrap unit lists that child path or the whole `fixtures/` directory.
This leaves separate feature owners able to add their cases while the
registry enforces shared ID, digest, parent-revision, and approval rules.
Bootstrap acceptance uses synthetic descriptors in memory; it does not take
ownership of another feature's fixture files.

Reference isolation is exercised as soon as dependency 001 exists. The
acceptance suite uses its production worker-context serialization and digest
path with synthetic, shape-compatible additions representing later retrieved
cards, source skills, and cached summaries. It rejects any reference answer,
label, fault description, reference ID, or reference digest in the serialized
worker packet. The test does not depend on 014/015/020 producers; their full
context integration remains an 018 and feature-owner check. This foundation
labels local-shell exposure `workflow-withheld; access isolation not
enforced` and does not claim filesystem isolation.

## Initial metric registry

Store every formula, numerator, denominator, favorable direction, minimum
cohort, threshold, applicable profile, fixture-set digest, evaluator version,
approval actor reference, and approval timestamp as explicit fields. Preserve
the accepted feature 018 gates exactly. The bootstrap test compares every
gate's formula, cohort, denominator rule, favorable direction, and threshold
against explicit accepted literals; it must not derive its expected values
from the registry or lock it is checking:

| Metric | Numerator / denominator | Direction and locked gate |
| --- | --- | --- |
| Answer quality | Correct outcome with supported or honestly uncertain interpretation / all reference questions | Higher; at least 0.90 with at least 20 questions |
| Unsupported promotion | Unsupported assertions presented as supported / assertions presented as supported | Lower; exactly 0 with at least 20 presented assertions |
| Fault recall | Planted faults preventing unsupported presentation / all planted faults | Higher; at least 0.95 with at least 20 faults |
| False-block rate | Valid reference claims blocked from supported presentation / all valid reference claims | Lower; at most 0.05 with at least 20 valid claims |
| Trace completeness | Displayed numerical assertions with a complete current Claim → Result → computation → transformation → data-version chain / all displayed numerical assertions | Higher; exactly 1.00 with at least 20 assertions |
| Budget completion | Questions settled within enforceable time and invocation limits / all reference questions | Higher; at least 0.90 with at least 20 questions |
| Answer retrieval Hit@3 | Queries with primary relevant card in top three / 20 frozen relevance queries over 150 cards | Higher; at least 0.90 |
| Ledger completeness | Admitted attempts with readable evidence-linked entries / all admitted attempts | Higher; exactly 1.00 with at least 20 attempts |
| Candidate precision | Correctly proposed positive signals / all proposed signals | Higher; at least 0.80 with at least 10 proposals |
| Candidate recall | Correctly proposed positive signals / all positive signals | Higher; at least 0.80 over all 32 positive signals |

Cost is reported as observed or unavailable and is not a numeric gate. The five
no-match queries have a separate fixed zero-false-direct-answer expected
outcome. Access leaks, invalid reference isolation, changed expected keys, and
unapproved threshold changes are zero-tolerance contract failures. The
registry must reject missing/zero or undersized required denominators and any
approval missing its configured owner reference or timestamp, or not strictly
earlier than the batch start. Feature 018 owns the final release calculation
and report.

## Work units and runnable acceptance

`tasks.md` divides the work into four units: implement the deterministic
fixture package, implement the registry/reference and contribution APIs,
freeze the lock/report, then run the bootstrap acceptance suite. Each
pre-final unit runs a named subset of
`src/analytics/acceptance/18-bootstrap.test.ts` for its own deliverable, as
well as `pnpm build`; the final unit runs the full suite. The full suite
verifies reproducible fixture IDs/digests and frozen development/evaluation
roles excluded from worker payloads; rejects reclassification of a tuned
development case; checks all ten accepted metric gates against literal values
and rejects missing, equal-time, or post-batch owner approvals and invalid
denominators; exercises an undersized disagreement audit as descriptive only
with no policy/threshold digest change; rejects mismatched comparative
settings; verifies frozen cohort sizes, direct and indirect reference
exposure rejection through 001's packet/digest path, append-only feature
evidence, and the accepted/unauthorized/colliding/late/exact-revision fixture
contribution cases. The runnable commands are `pnpm build`, a unit-specific
`pnpm exec tsx --test --test-name-pattern=<unit> src/analytics/acceptance/18-bootstrap.test.ts`,
and the full `pnpm exec tsx --test src/analytics/acceptance/18-bootstrap.test.ts`.

Requirement, success, and edge-case maps are empty because this is a build
foundation node rather than a feature specification. The 018 spec and accepted
thresholds remain unchanged.
