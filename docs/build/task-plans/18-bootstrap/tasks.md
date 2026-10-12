# 18-bootstrap tasks — Frozen synthetic cases and evaluator foundation

All checklist items are implementation work and remain unchecked during the
planning stage. Checklist IDs (`T001`–`T007`) are local implementation IDs;
proposal acceptance cases (`T01`–`T61`) remain distinct references.

This is the fixture/evaluator foundation build node, not the complete feature
018 release evaluator. Per the task-generation contract, its execution
manifest keeps requirement, success, and edge maps empty. The task-local
traceability below records the feature-spec obligations supplied by this
foundation and identifies whole-product obligations that remain with feature
018 and feature-owned evidence.

### Feature 018 FR/SC traceability at the foundation boundary

Every feature 018 FR and SC is accounted for below. “Foundation input” means
this node freezes the case, reference, denominator, or registry contract; it
does not claim to have run later feature behavior. The execution manifest's
FR/SC/edge maps remain empty as required for a nonfeature build node.

| Spec obligation | Foundation tasks | Boundary and later owner |
| --- | --- | --- |
| FR-001 / SC-001 complete question and evidence scoring/report | T005, T007 | Store response-bound adjudication and evidence inputs; complete scoring/report is feature 018. |
| FR-002 / SC-002 reference-key isolation | T001, T005, T007 | Separate worker/reference types and reject direct/indirect leaks in the actual 001 packet; local filesystem remains workflow-withheld. |
| FR-003 / SC-007 run configuration | T005, T007 | Require worker/tool/model, harness/version, policy/resource, source/prompt/context, fixture/rubric, and artifact refs in evidence. |
| FR-004 / SC-014 metrics in reports | T005, T007 | Preserve observed fields needed by the report; feature 018 computes/displays answer quality, unsupported assertions, fault recall, false blocks, trace, elapsed work, and cost. |
| FR-005 / SC-008 fair comparisons; SC-015 no holdout relabeling | T001, T005, T007 | Freeze roles/exposure and reject mismatched task constraints, harness/version, budget, fixture IDs, or adjudication protocol. |
| FR-006 / SC-003 pre-batch thresholds | T004, T006, T007 | Keep accepted literals fixed and require configured owner approval before batch start. |
| FR-007 / SC-004 memory evaluation | T003, T006, T007 | Freeze memory cohorts and denominators; 019/020/021 own behavior, and 018 owns the aggregate evaluation. |
| FR-008 / SC-004 automatic recall and reuse | T003, T006, T007 | Freeze the 150-card/20-query/five-no-match labels; 020 and 022 provide runtime behavior, scored later by 018. |
| FR-009 / SC-011 output discipline | T003, T007 | Freeze the 99-file import corpus and expected provenance limits; 019 owns import/catalog/output behavior and 018 reports it. |
| FR-010 / SC-012 ledger evaluation | T002, T005, T007 | Define admitted-attempt references and evidence fields; 023 owns ledger behavior and 018 scores it. |
| FR-011 / SC-013 metric registry | T004, T006, T007 | Register all ten formulas/cohorts/directions/minima/thresholds/profile/evaluator/fixture digest and pre-batch approval fields. |
| FR-012 / SC-005 fail-closed release input | T004, T007 | Reject missing, zero, or undersized denominators and unavailable required observations; feature 018 owns final release calculation. |
| FR-013 / SC-006 fault recall with false blocks | T002, T004, T007 | Seed planted faults and valid claims; register both distinct gates. Feature 018 tests the always-block failure against valid cases. |
| FR-014 / SC-009 versioned fixture/evaluator package | T001–T007 | Pin generator, source/grant, expected limits, faults, rubrics, evaluator, contributions, and lock revisions before execution. |
| FR-015 / SC-010 frozen-profile scope | T001, T002, T006, T007 | Freeze at least 20 evaluation questions and label the lock unscored; feature 018 permits only named-profile eligibility. |
| FR-016 / SC-016 review-disagreement audit | T005, T007 | Preserve the sampling frame and predeclared precision fields; undersized samples are descriptive and cannot alter policy/threshold digests. |

### Edge-case coverage at the foundation boundary

| Spec 018 edge case | Frozen FR/SC map | Foundation task | Foundation check; later owner boundary |
| --- | --- | --- | --- |
| 1. Benchmark answer key leaks into worker context | FR-002 / SC-015 | T005, T007 | Reject key/fault bytes in the serialized 001 worker packet and its digest, including future retrieved-card, source-skill, and cached-summary-shaped inputs. Full 018 batches repeat this against their live path. |
| 2. A tuned development case is called held out | FR-005 / SC-015 | T001, T007 | Fixture role and tuning exposure are immutable within a fixture-set revision; reject reclassification. |
| 3. Harness versions or budgets make comparisons unfair | FR-005 / SC-008 | T005, T007 | Compare only matching task constraints, harness/version, budget, fixture IDs, and adjudication protocol. |
| 4. Review-disagreement sample is too small | FR-016 / SC-016 | T005, T007 | Preserve the sampling frame and precision target, mark insufficient samples descriptive only, and assert the policy/threshold digest is unchanged. |

Proposal T01–T61 ownership remains in `docs/build/acceptance-plan.md`; this
foundation registers refs but does not take primary domain ownership. The
nonfeature manifest maps stay empty. Whole-product scoring, output/ledger
behavior, and release eligibility remain with feature 018 and the domain
owners listed in the acceptance plan.

### Edge-case coverage at the foundation boundary

| Spec 018 edge case | Foundation task | Foundation check; later owner boundary |
| --- | --- | --- |
| 1. Benchmark answer key leaks into worker context | T005, T007 | Reject key/fault bytes in the serialized 001 worker packet and its digest, including future retrieved-card, source-skill, and cached-summary-shaped inputs. Full 018 batches repeat this against their live path. |
| 2. A tuned development case is called held out | T001, T007 | Fixture role and tuning exposure are immutable within a fixture-set revision; reject reclassification. |
| 3. Harness versions or budgets make comparisons unfair | T005, T007 | Compare only matching task constraints, harness/version, budget, fixture IDs, and adjudication protocol. |
| 4. Review-disagreement sample is too small | T005, T007 | Preserve the sampling frame and precision target, mark insufficient samples descriptive only, and assert the policy/threshold digest is unchanged. |

## Phase 1 — Deterministic synthetic fixture package

### Story: Establish frozen, scoped cases that can be independently adjudicated

- [x] T001 Define versioned fixture types and deterministic generators for
  case IDs, canonical serialization, and SHA-256 digests. Separate worker
  inputs from evaluator references in the package types and loader boundary.
  Define synthetic source identity/version and explicit principal/grant
  profiles for aggregate, restricted row/sheet, and denied or expired access.
  Freeze each case role as `development` or `evaluation` in evaluator-only
  fixture metadata and the fixture-set digest, excluding the role from worker
  payloads. Record tuning exposure and make the role immutable within that set.
  Pin generation rules, seeds, expected-answer limits, planted-fault fields,
  adjudication rubric/version, and proposal-case references without taking
  ownership of another feature's T01–T61 outcomes.
  - Files: `src/analytics/evaluation/fixtures/index.ts`,
    `src/analytics/evaluation/fixtures/generators.ts`,
    `src/analytics/evaluation/fixtures/profiles.json`,
    `src/analytics/evaluation/fixtures/reference.json`,
    `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Depends on: graph prerequisite `001`.
  - Independent verification: `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-package src/analytics/acceptance/18-bootstrap.test.ts`.
  - Coverage: fixture schema, source/grant profiles, deterministic identity,
    frozen case roles, and runtime/evaluator boundary.

- [x] T002 Generate the initial question/evidence cohort from synthetic
  local data. Include at least 20 evaluation-role question cases and preserve
  valid, null/negative/inconclusive, and planted-fault outcomes; keep any
  development cases outside scored denominators. Seed the required
  denominators for 20 questions, 20 presented assertions, 20 faults, 20 valid
  claims, 20 displayed numerical assertions, and 20 admitted attempts. Specify
  scope, unit, denominator, numeric comparison rule, honest uncertainty, and
  supported-presentation limit for every reference case. Include binding,
  denominator/unit, omitted-check, leakage/exposure, and unavailable-evidence
  faults while preserving valid negative findings without favorable retries.
  - Files: `src/analytics/evaluation/fixtures/tabular.json`,
    `src/analytics/evaluation/fixtures/reference.json`,
    `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Depends on: T001.
  - Independent verification: `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-package src/analytics/acceptance/18-bootstrap.test.ts`.
  - Coverage: initial tabular profile evaluation cohort and denominator
    definitions; do not score product output in this unit.

- [x] T003 Freeze memory fixtures and adjudicated labels: 150 scoped cards,
  20 relevance queries with primary-card labels and scope distractors, five
  no-match queries including an empty-corpus query, the 99-file mixed
  CSV/workbook generator manifest, and the 48-signal capture set with 32
  positives and 16 negatives. Preserve per-item IDs, source/grant profile,
  generation rules, expected limits, planted variants where applicable, and
  rubric versions. No-match expected false direct answers are zero; duplicate
  capture proposals are counted incorrect.
  - Files: `src/analytics/evaluation/fixtures/retrieval.json`,
    `src/analytics/evaluation/fixtures/import-corpus.json`,
    `src/analytics/evaluation/fixtures/capture-signals.json`,
    `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Depends on: T001.
  - Independent verification: `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-package src/analytics/acceptance/18-bootstrap.test.ts`.
  - Coverage: fixed 150/20/5 retrieval cohorts, 99-file corpus, and
    48-signal precision/recall cohort.

## Phase 2 — Independent reference evaluator and append-only registry

### Story: Score after the worker response and accept later feature evidence

- [ ] T004 Implement immutable base metric declarations, ten accepted formulas/cohorts/directions/minima/thresholds, separate no-match contract and observed-or-unavailable non-gating cost. Validate required denominator inputs. Defer approval, batch and contribution transitions to their owning units.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T005 Validate and serialize the actual 001 worker context, including retrieved-card, source-skill and cached-summary additions. Reject direct and indirect answer-key, relevance-label, primaryRelevantCardId, reference digest and planted-fault leaks in permitted text fields as well as properties. Include a shape-valid summary containing the primary reference ID and a safe-summary positive control. Preserve actual packet digest.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T008 Preserve fixture role and exposure by immutable content/source lineage, not caller-provided IDs or exposure flags. Reject an exposed development payload copied under a new fixture ID and falsely declared unexposed evaluation; retain its exposure record. Distinct genuinely unexposed content remains eligible. Do not claim local-shell independence.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T009 Enforce configured owner, exact approval subject/digest and real approval time on metric/contribution approval records and at consumption. Reject missing/wrong owner, future approval even when a future batch start follows it, equal-time and post-start approval. Use one explicit clock boundary that is testable; never synthesize approval.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T010 Admit feature-owned descriptors only with configured owner, bound source/grant/content, exact parent revision and independently adjudicated expected outcome/rubric evidence. Reject invalid fixture shapes, changed-content ID collisions, missing independent evidence, unauthorized grant/owner and late registration. Append immutable child lock revisions without changing parent outcomes or thresholds. Consume exposure and approval validators already built.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T011 Pin each unique batch ID exactly once to the approved immutable registry revision, evaluator/profile/fixture/source/grant and fixed cohorts. Allow exact idempotent replay; reject changed reuse, late revision retroactive pinning, and selecting a later head. Reject fixed-cohort denominator drift and unavailable required denominators without deleting observations.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T012 Adjudicate only a bound completed worker response using evaluator-only reference data for the exact pinned fixture revision, including contributed fixtures. Reject mismatched task/harness/version/budget/source/grant/adjudication settings. Append evidence without rewriting lock or threshold digests. Validate disagreement sampling/precision fields; undersized audits remain descriptive.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

- [ ] T013 Exercise the complete registry through real serialization, contribution, approval, batch selection and post-response adjudication. Reproduce the actual prior review failures across composed APIs; require all registry tests to pass. Verify the original T004/T005 foundation obligations jointly and preserve all negative results. Do not implement later release aggregation.
  - Files: `src/analytics/evaluation/registry.ts`, `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Dependency: preceding scoped unit and existing feature prerequisites.
  - Verification: build plus the assigned scope tests; final feature acceptance retains the entire accepted contract.

## Phase 3 — Freeze lock and bootstrap acceptance

### Story: Make the declared fixture and test contract reviewable

- [ ] T006 Write the versioned fixture lock/report with all fixture IDs,
  generator versions/seeds, canonical digest rules and digests, cohort sizes,
  denominator definitions, metric formulas/gates, expected no-match outcome,
  owner-approval requirement, and reference-boundary disclosure. Mark the
  profile frozen and unscored; preserve a pending owner approval rather than
  inventing approval metadata. Include a versioning rule that additions create
  a new fixture-set version and cannot rewrite already evaluated references.
  - Files: `docs/build/evaluation-fixture-lock.json`,
    `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Depends on: T001–T005.
  - Independent verification: `pnpm build`; `pnpm exec tsx --test --test-name-pattern=fixture-lock src/analytics/acceptance/18-bootstrap.test.ts`.
  - Coverage: reproducible lock/report and pre-evaluation metric registration.

- [ ] T007 Add focused bootstrap acceptance cases for same-seed ID/digest
  reproducibility; lock and generator drift rejection; frozen `development`
  and `evaluation` case roles; at least 20 evaluation-role question cases;
  absence of role metadata from worker payloads; and rejection of relabeling a
  tuned development case as evaluation in the same fixture-set version. Check
  exact literals for
  all ten accepted gates: answer quality `>= 0.90` / 20 questions; unsupported
  promotion `= 0` / 20 presented assertions; fault recall `>= 0.95` / 20
  faults; false-block rate `<= 0.05` / 20 valid claims; trace completeness
  `= 1.00` / 20 displayed assertions; budget completion `>= 0.90` / 20
  questions; retrieval Hit@3 `>= 0.90` / 20 queries over 150 cards; ledger
  completeness `= 1.00` / 20 attempts; candidate precision `>= 0.80` / at
  least 10 proposals; and candidate recall `>= 0.80` / all 32 positive
  signals. Assert cost remains observed-or-unavailable and non-gating, and
  five no-match queries require zero false direct answers. Require matching
  configured-owner approval with a timestamp strictly earlier than batch
  start; reject missing, equal-time, and post-batch approval. Exercise an
  undersized review-disagreement sample against its predeclared precision
  target, assert all required audit fields and `descriptive_only`, then prove
  the policy/threshold digest did not change. Also verify exact cohort sizes
  and denominators and reject missing, zero, or undersized required
  denominators. For each of the ten registry entries, compare its
  exact metric ID, formula, numerator and denominator definition, favorable
  direction, minimum cohort, threshold, applicable profile, evaluator version,
  and fixture-set digest against the accepted registry contract. Reject
  comparative-run evidence when task constraints, harness/version, budget,
  fixture IDs, or adjudication protocol differs; matched comparisons retain
  identical fixture IDs and scoring-rubric references. Preserve the settings
  needed to explain each mismatch.
  Verify direct and indirect reference-exposure rejection, lock drift
  rejection, unchanged gates after evidence registration, and successful
  append-only registration of a later feature's execution evidence. Assert
  contribution acceptance before batch start; reject an unauthorized owner,
  changed-ID/content collision, missing independent expected outcomes, and
  approval or registry admission at/after batch start. Verify that parent
  lock, existing expected-outcome, and threshold digests remain unchanged and
  that evaluator selection uses exactly the contributed revision pinned by
  the batch even when a later revision exists. Run key/fault canaries through
  the 001 worker-context packet using synthetic future retrieved-card,
  source-skill, and cached-summary additions; reject any canary in the packet
  and prove the context digest binds only to the sanitized bytes. Assert that
  this foundation does not emit a final release assessment or broad
  reliability label.
  - Files: `src/analytics/acceptance/18-bootstrap.test.ts`.
  - Depends on: T001–T006.
  - Independent verification and final acceptance:
    `pnpm build`; `pnpm exec tsx --test src/analytics/acceptance/18-bootstrap.test.ts`.
  - Named acceptance: `18-bootstrap: deterministic fixture lock, isolated
    reference adjudication, declared denominators, and append-only evidence`.

## Unit ownership and dependency summary

| Unit | Tasks | Depends on | Deliverable |
| --- | --- | --- | --- |
| `fixture-package` | T001–T003 | None locally; graph node 001 | Deterministic synthetic package, frozen case roles, and frozen cohorts |
| `reference-registry` | T004–T005 | `fixture-package` | Exact metric registry, post-response adjudication, feature-scoped immutable contribution revisions, disagreement audit, and evidence append API |
| `fixture-lock-report` | T006 | `fixture-package`, `reference-registry` | `docs/build/evaluation-fixture-lock.json` |
| `bootstrap-acceptance` | T007 | `fixture-package`, `reference-registry`, `fixture-lock-report` | Focused acceptance suite and passing commands |

Every task appears in exactly one unit in `execution.json`. The final unit
runs the named acceptance and build; preceding units run deliverable-specific
acceptance subsets. No task changes accepted feature specs, checklists, policy
thresholds, reference answers, graph ownership, or runner configuration. The
manifest lists exact base fixture files and does not claim the feature-owned
contribution subtree or the whole fixtures directory.

## Coordinator decomposition amendment (2026-10-04)

The execution manifest defines the new bounded order and acceptance commands. Test-first setup stays with its production dependency. Each intermediate unit adds executable `scope:<unit-key>:` behavioral tests with positive and failure controls; final feature acceptance remains complete. Original obligations split across new IDs are retained in `docs/build/decomposition-audit.json`; coverage maps reference their full replacement task set. Existing requirement, threshold, interface and evidence rules remain normative.
