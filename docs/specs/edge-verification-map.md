# Edge-case verification map

Each edge bullet in a feature specification appears here in its source order.
The mapped FR owns its behavior; the numbered SC or proposal T-case gives a
fixture or acceptance scenario. These are verification contracts to implement,
not passing software tests. Changes to an Edge Cases list require updating
this map before its checklist can remain checked.

## 001

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A copied upstream document still contains software-factory examples. | FR-002 | SC-001 |
| 2 | A generic runner event uses the word “success” while analytical evidence fails. | FR-005 | SC-004 |
| 3 | A worker can search the repository broadly and encounter transitional modules. | FR-003 | SC-007 |
| 4 | An engine update changes a shared execution contract used by both products. | FR-006 | SC-005 |

## 002

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A task exits successfully without its declared result. | FR-006 | SC-002 |
| 2 | A checker writes malformed output or exits unexpectedly. | FR-003 | SC-008 |
| 3 | A validator reports “not applicable” without a policy-supported reason. | FR-004 | SC-008 |
| 4 | Two computations try to publish to the same logical result at once. | FR-007 | SC-004 |
| 5 | Byte serialization changes while the declared values remain equivalent. | FR-008 | SC-006 |
| 6 | Evidence is deleted or redacted after publication. | FR-010 | T22 |

## 003

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A join multiplies customer observations or a filter silently shifts the denominator. | FR-005 | SC-002 |
| 2 | A feature comes from after the prediction cutoff, a label is immature, or preprocessing uses evaluation data. | FR-007 | SC-002 |
| 3 | Evaluation metrics are opened during exploration. | FR-003 | T09 |
| 4 | The worker changes code or policy after a successful check. | FR-007 | SC-009 |
| 5 | A narrative report swaps a metric value, denominator, or unit. | FR-011 | SC-009 |
| 6 | Optional exploration consumes the work reserved for required checks. | FR-009 | T26 |
| 7 | A restarted worker receives an incomplete summary of earlier exposure. | FR-013 | T61 |

## 004

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Two environments have identical schemas but different metric meanings. | FR-008 | SC-002 |
| 2 | A source adds an unused column without changing the guidance a worker needs. | FR-010 | SC-009 |
| 3 | A stream or extract has a stale watermark or late corrections. | FR-010 | SC-002 |
| 4 | A tested join held in one sample but fails on another window. | FR-008 | SC-009 |
| 5 | A recipe needs more source privileges than the guide's intended audience has. | FR-005 | SC-008 |
| 6 | Source metadata or examples themselves reveal sensitive information. | FR-005 | SC-003 |

## 005

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A new window uses the same schema but changes business meaning. | FR-003 | SC-002 |
| 2 | A late event arrives after a report has already been consumed. | FR-004 | T20 |
| 3 | An internal answer is exported but not actually sent for publication. | FR-006 | SC-008 |
| 4 | A reviewer opens the trace but provides no substantive rationale. | FR-008 | SC-008 |
| 5 | A claim is inconclusive yet is useful as an internal answer. | FR-007 | SC-004 |
| 6 | A policy changes after an authorization was granted. | FR-010 | SC-005 |

## 006

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | One group is too small for the proposed uncertainty procedure. | FR-003 | SC-009 |
| 2 | Category membership is missing or changes across periods. | FR-007 | SC-008 |
| 3 | The sample is not representative of the population named in the question. | FR-003 | SC-009 |
| 4 | A worker infers gender or another sensitive group label from names. | FR-007 | SC-008 |
| 5 | The interval method cannot handle the observed dependence structure. | FR-003 | SC-009 |
| 6 | The worker uses an association to claim that changing group membership would cause renewal to change. | FR-008 | SC-005 |

## 007

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A join legitimately increases rows but not distinct customers. | FR-004 | SC-002 |
| 2 | Currency, timezone, key meaning, or category definitions change. | FR-006 | SC-007 |
| 3 | A source provides a query time but no repeatable snapshot. | FR-005 | SC-003 |
| 4 | An event arrives late or a target label is immature. | FR-002 | SC-008 |

## 008

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A source skill contains an earlier outcome and is loaded into the inquiry. | FR-003 | T25 |
| 2 | A code bug is repaired without changing the comparison. | FR-008 | SC-004 |
| 3 | A different harness resumes from a shortened summary. | FR-007 | SC-003 |
| 4 | A human approves post-observation work for exploratory use. | FR-006 | SC-003 |

## 009

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A completed command writes no declared output. | FR-002 | SC-001 |
| 2 | A worker reuses an earlier execution's path. | FR-001 | SC-005 |
| 3 | A crash occurs after commit but before the worker receives confirmation. | FR-004 | SC-002 |
| 4 | Serialization changes while values remain equal. | FR-005 | SC-003 |
| 5 | Evidence is later redacted under retention rules. | FR-006 | SC-004 |

## 010

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A checker crashes or writes malformed output. | FR-002 | SC-001 |
| 2 | A check passes on an older artifact or policy version. | FR-004 | SC-006 |
| 3 | A candidate fails to beat baseline while integrity checks pass. | FR-005 | SC-002 |

## 011

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Narrative text swaps two metrics or rounds away a material difference. | FR-002 | SC-002 |
| 2 | Generated HTML contains active markup or a path outside allowed artifacts. | FR-006 | SC-004 |
| 3 | The reader lacks permission to access the source data. | FR-007 | SC-005 |

## 012

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | An active task continues after a scheduling deadline. | FR-005 | SC-007 |
| 2 | Nested parallel computation exceeds reserved resources. | FR-011 | SC-009 |
| 3 | A software runner retries a valid negative result. | FR-004 | T04 |
| 4 | A replacement harness starts without prior exposure context. | FR-006 | SC-004 |

## 013

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Label maturity differs across cohorts. | FR-002 | SC-002 |
| 2 | A search candidate was declared before evaluation; another was added after. | FR-003 | SC-005 |
| 3 | The holdout was workflow-withheld but locally readable. | FR-007 | T24 |
| 4 | A better metric arises from a changed population or metric definition. | FR-001 | SC-006 |

## 014

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Identical table names have different meaning across environments. | FR-001 | SC-006 |
| 2 | A source definition is unknown or conflicts with an owner definition. | FR-007 | SC-004 |
| 3 | A test recipe is unbounded or requires stronger access than its audience. | FR-002 | SC-007 |
| 4 | Metadata itself is sensitive to a broader audience. | FR-008 | SC-005 |

## 015

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A skill carries holdout findings through a shared summary. | FR-005 | SC-003 |
| 2 | Access rights narrow after a skill was activated. | FR-006 | SC-005 |
| 3 | An old run pins a now-revoked skill version. | FR-004 | SC-007 |
| 4 | The model is shown a reference but never follows its recipe. | FR-002 | SC-007 |

## 016

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | An earlier window's data is no longer retained. | FR-005 | SC-008 |
| 2 | The same window has two corrections. | FR-004 | SC-009 |
| 3 | Event time and processing time differ materially. | FR-007 | SC-005 |
| 4 | Repeated monitoring tempts unlimited fresh significance tests. | FR-006 | SC-004 |

## 017

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A reviewer opens a trace but provides no substantive rationale. | FR-008 | SC-005 |
| 2 | A claim was outcome-informed and later approved for exploratory use. | FR-004 | SC-003 |
| 3 | An internal report is saved but never externally published. | FR-002 | SC-010 |
| 4 | Policy changes after an authorization. | FR-005 | SC-002 |
| 5 | The configured authority is revoked after an authorization. | FR-007 | SC-004 |

## 018

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | A benchmark answer key leaks into worker context. | FR-002 | SC-015 |
| 2 | The team tunes on a test case and later calls it held out. | FR-005 | SC-015 |
| 3 | Different harness versions or budgets make comparisons unfair. | FR-005 | SC-008 |
| 4 | A sample of review disagreements is too small for a stable estimate. | FR-016 | SC-016 |

## 019

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Two paths contain identical bytes but represent separate user-visible files. | FR-005 | SC-002 |
| 2 | An Excel workbook has several sheets; parsing some sheets is not permitted. | FR-015 | SC-009 |
| 3 | A filename says “final” but there is no evidence it was used. | FR-004 | SC-001 |
| 4 | A catalog update fails after evidence was committed. | FR-008 | SC-003 |
| 5 | A file contains sensitive values that must not enter a search index. | FR-007 | SC-008 |
| 6 | A local harness can write outside declared paths despite output contracts. | FR-014 | SC-007 |
| 7 | Cleanup is interrupted after deleting some eligible managed scratch files. | FR-016 | SC-010 |

## 020

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | One inquiry has several competing claims rather than one simple conclusion. | FR-008 | SC-005 |
| 2 | The original evidence has been redacted or is no longer available. | FR-005 | SC-003 |
| 3 | A previous holdout result is retrieved into a new inquiry. | FR-006 | SC-014 |
| 4 | Two answer cards refer to the same metric but different cohorts. | FR-014 | SC-012 |
| 5 | The worker exits or crashes after committing results but before writing prose. | FR-009 | SC-006 |
| 6 | Many superficially related cards compete for limited context. | FR-010 | SC-009 |

## 021

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | The user praises the answer but not the method or rule behind it. | FR-002 | SC-003 |
| 2 | A business calculation conflicts with an owner-curated definition. | FR-008 | SC-005 |
| 3 | A prior holdout outcome appears in the proposed skill body. | FR-004 | SC-008 |
| 4 | Candidate generation would exceed the inquiry's memory allowance. | FR-007 | SC-004 |
| 5 | A procedure depends on one source role or time period only. | FR-003 | SC-008 |

## 022

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | An active skill becomes stale after a source unit or access-role change. | FR-008 | SC-006 |
| 2 | A skill includes a prior evaluation outcome in its references. | FR-004 | SC-003 |
| 3 | A user explicitly approves a concrete rule and is its configured owner. | FR-003 | SC-004 |
| 4 | A later worker sees a skill entrypoint but does not execute its example. | FR-006 | SC-010 |
| 5 | A skill would require privileges the later worker does not have. | FR-005 | SC-008 |

## 023

| Edge | Case | Owning FR | Check |
| --- | --- | --- | --- |
| 1 | Two users phrase the same operational question differently. | FR-006 | SC-013 |
| 2 | Two analyses share a name but use different denominators or source roles. | FR-006 | SC-013 |
| 3 | One plan contains several executions or candidate models. | FR-012 | SC-008 |
| 4 | An infrastructure retry reruns the same command without changing the analytical question or plan. | FR-012 | SC-008 |
| 5 | A source is redacted after the ledger entry was written. | FR-011 | SC-006 |
| 6 | A dimension needed for exact-match assessment is unknown or unavailable. | FR-006 | SC-009 |
| 7 | A prior evaluation outcome is loaded during a new inquiry. | FR-009 | SC-007 |
| 8 | The search index is lost after durable attempt records are committed. | FR-010 | SC-004 |
| 9 | The ledger has no attempts yet, or none are visible under the worker's access scope. | FR-005 | SC-015 |
