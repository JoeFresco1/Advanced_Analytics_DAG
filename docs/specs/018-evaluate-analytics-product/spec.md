# Feature Specification: Evaluate Complete Analytics Work

**Feature Branch**: `main` (specification only)

**Created**: 2026-09-27

**Status**: Draft

**State vocabulary**: [Canonical analytics states](../../docs/specs/state-vocabularies.md).

**Input**: Evaluate whole question-to-answer behavior and seeded failures before describing a feature as reliable or ready to release.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - See a release-quality assessment (Priority: P1)

A product owner can tell how often the worker answers reference questions
correctly, qualifies uncertainty, catches planted errors, and exposes evidence
within a declared allowance.

**Why this priority**: Passing isolated module tests does not demonstrate a
reliable analytical worker.

**Independent Test**: Run a fixed batch of valid, null, and faulted questions
with recorded worker settings and reference answers kept outside its context.

**Acceptance Scenarios**:

1. **Given** a declared evaluation batch, **when** it completes, **then** the
   owner sees answer quality, unsupported claims, caught faults, false blocks,
   trace completeness, and budget usage with denominators.
2. **Given** missing cost telemetry, **when** metrics appear, **then** cost
   is unavailable rather than treated as zero.
3. **Given** a batch that misses any required release threshold, **when** the
   owner inspects the assessment, **then** release is blocked for that profile
   and the failed metric, numerator, denominator, and threshold are shown.

### User Story 2 - Compare source memory value (Priority: P2)

An analyst can tell whether using a validated source skill reduces repeated
discovery without degrading answer quality or required checks.

**Why this priority**: The source-memory investment needs evidence of value.

**Independent Test**: Run comparable cold-source and warm-source inquiries
under the same declared question and allowance.

**Acceptance Scenarios**:

1. **Given** cold and warm runs, **when** compared, **then** the feature 015
   discovery-step count, answer quality, and skipped-check counts are reported
   together.

### Edge Cases

- A benchmark answer key leaks into worker context.
- The team tunes on a test case and later calls it held out.
- Different harness versions or budgets make comparisons unfair.
- A sample of review disagreements is too small for a stable estimate.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Evaluations MUST score complete questions and evidence states,
  not only task counts or command exits.
- **FR-002**: Reference answers and planted fault definitions MUST remain
  outside the worker's allowed context during the evaluated run.
- **FR-003**: Each run MUST record question fixture, worker settings,
  allowance, source version, and evaluator rules.
- **FR-004**: Reports MUST include correct or appropriately uncertain
  answers, unsupported assertions, caught faults, false blocks, trace
  completeness, elapsed work, and observed or unavailable cost.
- **FR-005**: Comparative claims MUST use the same task constraints and
  distinguish development cases from held-out cases.
- **FR-006**: Numerical release thresholds MUST be declared before the
  evaluation batch they govern.
- **FR-007**: The product memory evaluation MUST measure whether people can
  retrieve past answers by question, how many committed outputs are cataloged,
  whether irrelevant files become false stories, and whether proposed skills
  are validated and later reused.
- **FR-008**: Memory evaluation MUST measure automatic recall on related later
  inquiries, missed relevant answers, and incorrect reuse across incompatible
  populations, periods, or source versions.
- **FR-009**: Output evaluation MUST measure retained file count and bytes,
  unwanted CSV/workbook exports, notebook replay status, and files written
  outside the managed root under each supported execution profile.
- **FR-010**: Ledger evaluation MUST measure attempt-entry completeness,
  exact-repeat detection, false duplicate warnings on new data, and whether
  prior negative or failed work is retrieved before a later related analysis.
- **FR-011**: A versioned metric registry MUST define each metric's formula,
  numerator, denominator, favorable direction, minimum cohort size, threshold,
  applicable profile, and approval actor and timestamp before the batch starts.
  The configured project owner under feature 010 FR-008 approves the registry
  version; the worker cannot approve its own release threshold.
- **FR-012**: A release assessment MUST fail closed when a required metric
  misses its threshold, its denominator is too small or zero, the threshold
  was set after the batch, or a required observation is unavailable. The
  product MUST NOT describe that profile as validated after a failed batch.
- **FR-013**: Fault recall MUST be reported alongside false-block rate on
  valid cases; an evaluator that blocks every claim MUST NOT pass the release
  gate solely by catching every planted fault.
- **FR-014**: Feature 018 MUST own a versioned synthetic fixture package and
  independent reference evaluator. Each case MUST define its generation
  rule, source/grant profile, expected answer limits, planted faults,
  adjudication rubric, and evaluator version before worker execution.
  Feature specs MAY contribute cases but MUST NOT author the expected answer
  during the evaluated run. Held-out cases remain outside worker context.
- **FR-015**: A batch satisfying the initial minimum of 20 cases is eligible
  only for the named frozen-fixture profile, not a general reliability claim.
  Any broader reliability claim MUST predeclare its target population,
  sample-size rationale, uncertainty interval, and separate held-out batch.
- **FR-016**: Review-disagreement audits MUST record the sampling frame,
  policy version, risk strata, selection rule, adjudicator, disagreement
  severity, numerator, denominator, and uncertainty. A sample too small for
  the predeclared precision target MUST be labeled descriptive only and MUST
  NOT automatically change a policy threshold.

### Key Entities

- **Reference case**: Question, data, known answer limits, and fault variants.
- **Evaluation run**: Worker and allowance settings plus measured outcomes.
- **Release assessment**: Thresholds, results, and unresolved failures.
- **Metric registry entry**: Formula, cohort, direction, threshold, minimum
  denominator, and pre-batch approval time.

### Initial metric registry for the first tabular profile

These thresholds are the initial profile's declared release gate, not a claim
that all future domains share the same acceptable error rate. Every rate uses
the named frozen fixture cohort; undefined denominators fail the gate.

| Metric | Numerator / denominator | Favorable direction | Gate |
| --- | --- | --- | --- |
| Answer quality | Correct outcome and supported-or-honestly-uncertain interpretation / all reference questions | Higher | At least 0.90 on at least 20 questions |
| Unsupported promotion | Unsupported assertions presented as supported / assertions presented as supported | Lower | Exactly 0 on at least 20 presented assertions |
| Fault recall | Planted faults that prevent unsupported presentation / all planted faults | Higher | At least 0.95 on at least 20 faults |
| False-block rate | Valid reference claims blocked from supported presentation / all valid reference claims | Lower | At most 0.05 on at least 20 valid claims |
| Trace completeness | Displayed numerical assertions with a complete current Claim → Result → computation → transformation → data-version chain / all displayed numerical assertions | Higher | Exactly 1.00 on at least 20 assertions |
| Budget completion | Questions settled within declared enforceable time and invocation limits / all reference questions | Higher | At least 0.90 on at least 20 questions |
| Answer retrieval Hit@3 | Queries whose annotated primary relevant card is in top three / 20 frozen relevance queries | Higher | At least 0.90; use feature 020's 150-card fixture |
| Ledger completeness | Admitted analytical attempts with readable evidence-linked entries / all admitted analytical attempts | Higher | Exactly 1.00 on at least 20 attempts |
| Candidate precision, capture profile | Correctly proposed positive signals / all proposed signals | Higher | At least 0.80 on at least 10 proposals in 021's frozen 48-signal fixture |
| Candidate recall, capture profile | Correctly proposed positive signals / all positive signals | Higher | At least 0.80 on all 32 positive signals in 021's frozen fixture |

Cost is reported as observed or unavailable and is not a numerical release
gate until its telemetry and cap can actually be enforced. Access leaks,
invalid benchmark isolation, and unapproved threshold changes are separate
zero-tolerance contract failures. The metric registry stores the exact fixture
set, evaluator version, and timestamp used for each gate.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Every evaluated report includes numerators and denominators
  for answer quality, unsupported claims, fault detection, and false blocking.
- **SC-002**: Zero benchmark keys or planted fault descriptions are supplied
  to the worker during a held-out run.
- **SC-003**: All release thresholds used to judge a batch have timestamps
  preceding that batch; no post-hoc threshold change is hidden.
- **SC-004**: The crowded-file and repeated-method fixtures report answer
  retrieval success, catalog completeness, false story promotion, skill
  proposal precision, later skill use, automatic recall, and incorrect reuse
  with explicit denominators.
- **SC-005**: A frozen batch meeting every initial registry gate and minimum
  denominator is marked eligible for the evaluated profile; each seeded
  missed-threshold, missing-observation, or post-batch-threshold case is denied
  with the exact failed calculation visible.
- **SC-006**: An always-block evaluator catches all planted faults but fails
  the false-block gate on valid cases.

- **SC-007**: Every benchmark run in the frozen batch records the complete
  worker, tool, model, policy, and resource settings needed to reproduce its
  evaluation configuration.
- **SC-008**: A proposed comparative report with mismatched task constraints
  or adjudication protocol fails comparison validation; matched runs retain
  the same fixture IDs and scoring rubric.
- **SC-009**: Every scored case resolves to a pinned fixture generator,
  reference evaluator, expected-answer rubric, and grant profile. Mutating a
  reference answer after the batch starts invalidates that batch.
- **SC-010**: A 20-case passing batch is labeled only as eligible for its
  frozen fixture profile; a broad reliability claim with no predeclared
  population and uncertainty estimate is rejected.
- **SC-011**: For each supported execution profile, the evaluation reports
  retained file count and bytes, unwanted CSV/workbook export count,
  notebook replay status, and unmanaged-root writes with denominators or
  explicit not-applicable reasons.
- **SC-012**: The ledger batch reports entry completeness, exact-repeat
  detection, false duplicate warnings on new data, and retrieval of prior
  negative or failed attempts with the relevant denominators.
- **SC-013**: Every metric used for release resolves to its frozen formula,
  numerator, denominator, direction, minimum cohort, threshold, profile,
  owner approval actor, and pre-batch timestamp; a missing field fails the
  release assessment.
- **SC-014**: Each evaluation report shows answer quality, unsupported
  assertions, caught faults, false blocks, trace completeness, elapsed work,
  and observed cost or `unavailable`, with the relevant denominator or unit.
- **SC-015**: A development case reused for tuning cannot be reclassified as
  held-out in the same evaluation version; a leaked answer key invalidates
  the batch and blocks its release assessment.
- **SC-016**: A small review-disagreement sample reports its frame, selection,
  adjudication, counts, and uncertainty as descriptive only; it produces zero
  automatic policy-threshold changes or broad calibration claims.

## Assumptions

- Early batches may be too small to establish broad statistical reliability;
  the report states that limit.
- Implementation performance is measured as each feature lands, then again
  across complete inquiries.

**Proposal trace**: §§18–21; evaluates frozen cases contributed by the
owning features rather than claiming domain ownership of T01–T61;
I14–I17, I19–I20.
