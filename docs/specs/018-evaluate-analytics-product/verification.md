# Verification Plan: Whole-Product Evaluation

**Stage**: Planning evidence map. No product implementation or acceptance run
has occurred in this planning task. The final implementation task T008 must
replace the planned evidence references below with actual command outputs,
batch and lock references, metric counts, and committed evidence links. This
file intentionally makes no pass/fail claim before those observations exist.

## Acceptance commands and runtime evidence

T008 runs `pnpm test` (which includes `src/analytics/acceptance/018.test.ts`)
and `pnpm smoke:viewer`. The acceptance test must call the production worker
and controller on held-out synthetic fixtures with model `gpt-6-luna` and
reasoning `xhigh`; no answer fixture or expected-answer stub may stand in for
the worker. T008 records the actual batch ID, registry/fixture-lock digest,
worker and harness versions, budgets, command exit codes, exact numerators and
denominators, and the `EvidenceRef`s for each case and report.

The evidence profile is labeled `workflow-withheld; access isolation not
enforced` unless the actual harness proves a stronger boundary. A key/fault
leak or changed reference digest invalidates the batch. Metric failures remain
visible with their exact counts, threshold, minimum and references. Unknown
cost remains `unavailable`.

## FR-to-evidence map

| Requirement | Executable task(s) | Test/evidence contract | Actual evidence T008 must record |
| --- | --- | --- | --- |
| FR-001 | T002, T006, T008 | A01 scores whole questions, claims and current evidence states, rather than process/task status; each scored assertion resolves to its evidence chain. | Batch/case refs; answer and evidence-state counts; assertion/evidence refs and denominator. |
| FR-002 | T001, T006, T007, T008 | A05 audits the actual worker packet and digest for zero answer-key and planted-fault-description bytes; evaluator access occurs only after response. | Context packet digest, isolation audit result and batch validity evidence. |
| FR-003 | T001, T002, T006, T008 | A16 checks fixture, worker, harness, model, reasoning, allowance, source version, policy and evaluator versions on each case. | Batch configuration record and per-case setting refs. |
| FR-004 | T002, T003, T006, T008 | A01/A02 report answer quality, unsupported claims, caught faults, false blocks, trace completeness, elapsed work and observed/unavailable cost with denominators. | Structured report values, counts, units, evidence refs and cost telemetry state. |
| FR-005 | T004, T007, T008 | A06/A07 separate development and held-out cases and reject unequal task, fixture, source, harness, budget or adjudication protocols. | Comparison validation and frozen development/held-out fixture IDs. |
| FR-006 | T004, T007, T008 | A09 rejects any metric threshold whose approved registry version/time is not prior to batch start. | Registry version, configured owner approval actor/time, batch start time and gate result. |
| FR-007 | T003, T007, T008 | A12/A13 score Hit@3 >= 0.90 on 20 queries against 150 cards, zero false direct answers on five no-match queries, the 99-file catalog/false-story cohort, and candidate precision >= 0.80 on at least 10 proposals plus recall >= 0.80 over all 32 positive signals in the 48-signal skill cohort. | Query/card, no-match and import artifact counts; candidate and reuse numerators/denominators; fixture refs. |
| FR-008 | T003, T007, T008 | A12/A13 report automatic recall, missed relevant answers and incompatible population/period/source-version reuse. | Retrieval and reuse case refs, correct/missed/incompatible counts and denominators. |
| FR-009 | T003, T007, T008 | A14 reports retained files/bytes, unwanted CSV/workbook exports, notebook replay and unmanaged-root writes per supported execution profile. | Per-profile counts/bytes, denominators and explicit not-applicable reasons. |
| FR-010 | T003, T007, T008 | A15 reports attempt-entry completeness, exact repeat detection, false duplicate warnings on changed data and prior negative/failed attempt retrieval. | Ledger attempt and match refs with each numerator/denominator. |
| FR-011 | T004, T005, T007, T008 | A09 validates the full versioned metric entry: formula, numerator, denominator, direction, minimum, threshold, profile, configured project-owner approval under feature 010 FR-008, approval timestamp and evaluator/fixture refs. | Frozen registry entry IDs/digests and field-by-field validation output. |
| FR-012 | T005, T007, T008 | A03/A09 deny release on missed gate, under/zero denominator, unavailable required observation, post-batch approval or invalid reference. | Exact failed calculations, reasons, evidence refs and fixture-profile decision. |
| FR-013 | T002, T005, T007, T008 | A11 proves an always-block evaluator can catch planted faults and still fail the false-block gate on valid claims. | Fault recall and valid-claim false-block numerators/denominators and gate outcomes. |
| FR-014 | T001, T006, T007, T008 | A10 pins each case to generator, source/grant profile, reference evaluator, rubric and version; reference mutation invalidates the batch. | Fixture IDs/digests, grant refs, evaluator/rubric versions and mutation result. |
| FR-015 | T001, T005, T007, T008 | A06 denies reclassifying a tuning/development case as held out; A17 rejects a broad reliability request missing the population, sample rationale, uncertainty interval or separate batch. | Profile and general-reliability decisions with exact missing fields and case partition refs. |
| FR-016 | T004, T007, T008 | A08 records disagreement sampling frame, policy version, risk strata, selection, adjudicator, severity, counts and uncertainty; too-small samples remain descriptive and cannot change thresholds. | Sample selection/audit record, target precision, achieved denominator, interval, policy before/after refs. |

## SC-to-evidence map

| Success criterion | Executable task(s) | Test/evidence contract | Actual evidence T008 must record |
| --- | --- | --- | --- |
| SC-001 | T002, T006, T008 | A01 report has numerator/denominator for answer quality, unsupported claims, caught faults and false blocks. | Four metric observations and their evidence refs. |
| SC-002 | T001, T006, T008 | A05 records zero key/fault-description bytes supplied to held-out worker. | Actual context digest and exclusion audit. |
| SC-003 | T004, T007, T008 | A09 proves every applied threshold approval predates batch start. | Approval and batch timestamps plus comparison result. |
| SC-004 | T003, T007, T008 | A12/A13 cover Hit@3 >= 0.90 on the 150-card/20-query cohort, five no-match queries, the 99-file catalog/false-story cohort, skill-candidate precision/recall at the locked 48-signal denominators, later skill use, automatic recall and incompatible reuse. | Frozen cohort IDs and all metric counts. |
| SC-005 | T005, T007, T008 | A03 includes seeded missed-threshold, missing observation and post-batch approval; denial shows exact calculation. | Each failure case's metric counts, threshold and linked evidence. |
| SC-006 | T002, T005, T007, T008 | A11 always-block case catches planted faults but fails false-block threshold on valid claims. | Both rates and both gate decisions. |
| SC-007 | T001, T002, T006, T008 | A16 captures worker/tool/model/policy/resource configuration for each benchmark inquiry. | Full run configuration refs and digests. |
| SC-008 | T004, T007, T008 | A07 rejects mismatched task or adjudication setup; matched comparison keeps the same fixture IDs and rubric. | Difference fields for rejection and matched pair refs. |
| SC-009 | T001, T006, T007, T008 | A10 resolves fixture generator, answer rubric, evaluator version, grant profile and immutable digest; post-start mutation invalidates. | Pinned reference IDs/digests and mutation invalidation evidence. |
| SC-010 | T005, T007, T008 | A17 accepts only the named fixture profile from the 20-case gate and rejects an unsupported general claim. | Separate eligibility fields and broad-claim denial reasons. |
| SC-011 | T003, T007, T008 | A14 reports retained count/bytes, exports, notebook replay and unmanaged writes for each execution profile. | Per-profile observation records and denominators/reasons. |
| SC-012 | T003, T007, T008 | A15 scores ledger completeness, exact repeats, false duplicates and retrieval of negative/failed work. | Ledger cohort refs and four metric calculations. |
| SC-013 | T004, T005, T007, T008 | A09 resolves every release metric to its frozen formula, counts, direction, minimum, threshold, profile, configured owner under 010 FR-008 and pre-batch time. | Registry field audit and immutable approval references. |
| SC-014 | T002, T003, T006, T008 | A01/A02 report required answer/evidence/fault/work/cost fields with counts, units or `unavailable`. | Final metric report and case-level support references. |
| SC-015 | T001, T004, T006, T007, T008 | A05/A06/A10 detect leaked keys, invalid tuning/holdout relabeling and changed reference digests. | Batch invalidation or partition evidence with fixture versions. |
| SC-016 | T004, T007, T008 | A08 labels insufficient disagreement samples descriptive, reports uncertainty and makes no threshold update. | Sampling record, interval, descriptive-only flag and unchanged policy digest. |

## Ordered Edge Case evidence map

The following order matches the four Edge Cases bullets in `spec.md`.

| Edge ID / case | Owning requirement | Executable task(s) | Test/evidence contract | Actual evidence T008 must record |
| --- | --- | --- | --- | --- |
| EC-001: A benchmark answer key leaks into worker context. | FR-002 | T001, T006, T007, T008 | A05 injects a leak sentinel into a denied/evaluator-only context path; the batch is invalid and reports the context/reference evidence. | Actual packet digest, leak finding, invalidated batch and no eligible release. |
| EC-002: A tuned test case is later called held out. | FR-005 | T001, T004, T006, T007, T008 | A06 uses the frozen development/held-out partition and rejects reclassification within the same evaluation version. | Fixture partition refs and rejected comparison/release reason. |
| EC-003: Harness versions or budgets differ. | FR-005 | T001, T004, T007, T008 | A07 rejects unequal harness/model/task allowance or budget; matched runs preserve fixture IDs and scoring rubric. | Exact mismatched fields and matched-run identifiers. |
| EC-004: Review-disagreement sample is too small. | FR-016 | T004, T007, T008 | A08 reports the frozen frame and observed disagreement counts/uncertainty as descriptive only; no policy threshold changes. | Sample size, predeclared precision target, uncertainty, descriptive-only decision and unchanged policy reference. |

## Planned test inventory

`src/analytics/acceptance/018.test.ts` will define the named A01-A17
assertions used above. The test must consume bootstrap-owned fixture IDs and
must not define independent expected answers, policy thresholds, or grant
profiles. T008 records actual run evidence in each row above and identifies
any failed or unavailable scenario rather than checking it off with a
placeholder.
