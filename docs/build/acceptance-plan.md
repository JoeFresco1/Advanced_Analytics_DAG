# Acceptance plan

**Status:** frozen strategy for the existing 26-node build graph  
**Date:** 2026-09-28  
**Scope:** accepted FR/SC and edge-case maps, graph boundaries, proposal T-case primary ownership, user paths, evaluator controls, and release checks. Feature specifications, checklists, expected answers, policy thresholds, and labels remain unchanged.

## What acceptance proves

Acceptance combines three levels:

1. **Structural contract coverage:** `fr-verification-map.md` maps exactly one primary numbered SC or proposal T case to each of the 234 FRs. `edge-verification-map.md` maps each of the 115 feature Edge Cases to its owning FR and check in source order. `traceability-audit.md` assigns exactly one primary focused-spec owner to each T01–T61. These existing maps are normative and are consumed without rewriting the criteria here.
2. **Feature behavior:** each graph owner implements and runs its own feature acceptance test, reporting the actual FR/SC/edge/T IDs and observed result. Deterministic tests cover schema, identity, policy, access, budget, path, and state transitions. Every feature slice includes a producer, controller consumer, viewer/user path, and seeded failure; a unit test by itself does not close a boundary.
3. **Integrated product behavior:** integration nodes drive the real producer through its focused facade and consumer to the browser or CLI result, then inject a failure and verify its retained evidence and user-visible consequence. Whole-product evaluation runs only after the worker response and reports locked denominators and thresholds.

A passing process is not an analytical pass. A valid null, negative, inconclusive, conflicting, failed-check, or incomplete attempt stays in the record. Tests of neutral scheduling prove execution compatibility only; they do not prove measurement validity or claim support.

## Accepted FR and edge-case registers

The direct FR-to-primary-check mapping is the unchanged [FR verification map](../specs/fr-verification-map.md). All numbered FRs appear there once, with their existing `SC-*` or `T*` primary check. The following index binds the feature contracts and their full FR/SC map entries to their graph owner without copying or weakening an accepted criterion:

| Feature spec | FR IDs / count | Edge cases / count | Primary-check register |
| --- | --- | --- | --- |
| [001](../../specs/001-isolate-analytics-product/spec.md) | FR-001–FR-011 (11) | 4 | [FR map §001](../specs/fr-verification-map.md#001); [edge map §001](../specs/edge-verification-map.md#001) |
| [002](../../specs/002-executable-evidence/spec.md) | FR-001–FR-010 (10) | 6 | [FR map §002](../specs/fr-verification-map.md#002); [edge map §002](../specs/edge-verification-map.md#002) |
| [003](../../specs/003-delegated-inquiry/spec.md) | FR-001–FR-014 (14) | 7 | [FR map §003](../specs/fr-verification-map.md#003); [edge map §003](../specs/edge-verification-map.md#003) |
| [004](../../specs/004-source-memory/spec.md) | FR-001–FR-011 (11) | 6 | [FR map §004](../specs/fr-verification-map.md#004); [edge map §004](../specs/edge-verification-map.md#004) |
| [005](../../specs/005-recurrence-and-use/spec.md) | FR-001–FR-011 (11) | 6 | [FR map §005](../specs/fr-verification-map.md#005); [edge map §005](../specs/edge-verification-map.md#005) |
| [006](../../specs/006-inferential-profile/spec.md) | FR-001–FR-011 (11) | 6 | [FR map §006](../specs/fr-verification-map.md#006); [edge map §006](../specs/edge-verification-map.md#006) |
| [007](../../specs/007-data-identity-measurement/spec.md) | FR-001–FR-007 (7) | 4 | [FR map §007](../specs/fr-verification-map.md#007); [edge map §007](../specs/edge-verification-map.md#007) |
| [008](../../specs/008-plan-exposure-history/spec.md) | FR-001–FR-009 (9) | 4 | [FR map §008](../specs/fr-verification-map.md#008); [edge map §008](../specs/edge-verification-map.md#008) |
| [009](../../specs/009-publish-evidence/spec.md) | FR-001–FR-006 (6) | 5 | [FR map §009](../specs/fr-verification-map.md#009); [edge map §009](../specs/edge-verification-map.md#009) |
| [010](../../specs/010-run-required-checks/spec.md) | FR-001–FR-008 (8) | 3 | [FR map §010](../specs/fr-verification-map.md#010); [edge map §010](../specs/edge-verification-map.md#010) |
| [011](../../specs/011-inspect-result-trace/spec.md) | FR-001–FR-008 (8) | 3 | [FR map §011](../specs/fr-verification-map.md#011); [edge map §011](../specs/edge-verification-map.md#011) |
| [012](../../specs/012-bound-worker-inquiry/spec.md) | FR-001–FR-011 (11) | 4 | [FR map §012](../specs/fr-verification-map.md#012); [edge map §012](../specs/edge-verification-map.md#012) |
| [013](../../specs/013-compare-predictive-value/spec.md) | FR-001–FR-008 (8) | 4 | [FR map §013](../specs/fr-verification-map.md#013); [edge map §013](../specs/edge-verification-map.md#013) |
| [014](../../specs/014-build-source-skill/spec.md) | FR-001–FR-008 (8) | 4 | [FR map §014](../specs/fr-verification-map.md#014); [edge map §014](../specs/edge-verification-map.md#014) |
| [015](../../specs/015-reuse-source-skill/spec.md) | FR-001–FR-006 (6) | 4 | [FR map §015](../specs/fr-verification-map.md#015); [edge map §015](../specs/edge-verification-map.md#015) |
| [016](../../specs/016-reconcile-recurring-windows/spec.md) | FR-001–FR-007 (7) | 4 | [FR map §016](../specs/fr-verification-map.md#016); [edge map §016](../specs/edge-verification-map.md#016) |
| [017](../../specs/017-authorize-claim-use/spec.md) | FR-001–FR-009 (9) | 5 | [FR map §017](../specs/fr-verification-map.md#017); [edge map §017](../specs/edge-verification-map.md#017) |
| [018](../../specs/018-evaluate-analytics-product/spec.md) | FR-001–FR-016 (16) | 4 | [FR map §018](../specs/fr-verification-map.md#018); [edge map §018](../specs/edge-verification-map.md#018) |
| [019](../../specs/019-catalog-analytical-artifacts/spec.md) | FR-001–FR-017 (17) | 7 | [FR map §019](../specs/fr-verification-map.md#019); [edge map §019](../specs/edge-verification-map.md#019) |
| [020](../../specs/020-find-analytical-answers/spec.md) | FR-001–FR-017 (17) | 6 | [FR map §020](../specs/fr-verification-map.md#020); [edge map §020](../specs/edge-verification-map.md#020) |
| [021](../../specs/021-detect-skill-candidates/spec.md) | FR-001–FR-009 (9) | 5 | [FR map §021](../specs/fr-verification-map.md#021); [edge map §021](../specs/edge-verification-map.md#021) |
| [022](../../specs/022-activate-reusable-skills/spec.md) | FR-001–FR-008 (8) | 5 | [FR map §022](../specs/fr-verification-map.md#022); [edge map §022](../specs/edge-verification-map.md#022) |
| [023](../../specs/023-record-analysis-ledger/spec.md) | FR-001–FR-012 (12) | 9 | [FR map §023](../specs/fr-verification-map.md#023); [edge map §023](../specs/edge-verification-map.md#023) |

Totals are 234 FRs and 115 edge cases. Their definitions, wording, ordering, and criterion links remain in the existing accepted specs and maps. The per-feature requirement checklists remain specification-quality records and are not treated as implementation test results.

## Exact proposal T-case primary ownership

This table is the primary owner assignment from the [traceability audit](../specs/traceability-audit.md). A feature may exercise another owner's case as supporting integrated evidence, but only the row below owns the primary focused acceptance. The lists are exact; no inclusive range is used for the memory slice.

| Primary feature owner | Exact primary T cases |
| --- | --- |
| 001 | T53 |
| 007 | T05 |
| 008 | T06, T07, T09, T61 |
| 009 | T01, T02, T13, T14, T15, T22, T52 |
| 010 | T03, T10, T23 |
| 011 | T11, T60 |
| 012 | T12, T26, T27 |
| 013 | T04, T08, T24 |
| 014 | T17 |
| 015 | T18, T19, T25 |
| 016 | T20, T58 |
| 017 | T16, T21 |
| 019 | T28, T29, T30, **T37**, T43, T44, T45, T46, T47 |
| 020 | T31, T35, T36, T39, T40, T41, T42, T56, T57 |
| 021 | T32, **T38** |
| 022 | T33, T34, T59 |
| 023 | T48, T49, T50, T51 |
| 006 | T54, T55 |

The registry is a one-to-one partition of T01–T61. Feature 019 owns catalog rebuild T37; feature 021 owns capture T38. Feature 020's exact list excludes both cases. Feature 018 owns evaluation and release implementation and scores frozen cases; it does not take primary domain ownership from the rows above.

## Graph-boundary acceptance register

The following register covers every node and dependency from [graph.json](graph.json). Output paths, per-file owners, and acceptance-test filenames remain exactly those in the graph. “Failure to inject/observe” describes the minimum end-to-end obligation owned by or consumed at that boundary.

| Node | Depends on | Owned facade or boundary | Minimum acceptance path and injected failure |
| --- | --- | --- | --- |
| 00 | — | Three architecture outputs, frozen interface document, acceptance map | Audit graph ownership/dependencies, T-case owners, placeholder scan, and structural coverage; no product action. |
| 001 | 00 | Principal/context and neutral-runner adapter | Live analytics worker context + neutral task. Verify zero factory context, same neutral conformance suite, access deny/allow, retry/review/fallback disabled. Build the local neutral package, real archive digest, compatibility adapter and Python contract bootstrap; remote publication is not required. |
| 18-bootstrap | 001 | Fixture registry, fixtures, fixture lock, base evaluator API | Show fixture/grant schema and worker packet contain zero reference answer/fault-description bytes; evaluator reads keys only after response. |
| 009 | 001, 18-bootstrap | Run/project store, publication/CAS/recovery and single-stage coordinator | Producer computation → atomic commit → 011 trace. Inject pre-commit crash, lost acknowledgement, changed/missing output, same-revision race, and stale identity; partial output never appears committed. Test both scopes, same-ID changed-content rejection, and project refs to uncommitted run records. |
| 010 | 009 | Frozen method policy and CheckResult | Run check on exact subjects → assess dependent Claim → 011 display. Inject overlap, malformed/missing/stale check, unapproved `not_applicable`, and valid negative result; no favorable retry. |
| 011 | 009, 010 | Report, HTTP API, analytics viewer | Open answer first, then numerical assertion trace. Inject value/denominator swap, active HTML, traversal/symlink path, and viewer grant denial. |
| 002 | 009, 010, 011, 18-bootstrap | Integrated fixed-evidence demo and synthetic Python calculation fixture | Invoke the real fixed-analysis Python producer through 009 stages.ts and follow evidence through publication/checks to browser; 003 is not a prerequisite; failure demonstrates remain durable and readable. |
| 007 | 009, 18-bootstrap | Source identity, measurement and descriptive Python/TS facade | Acquire synthetic version → transform → descriptive count/sum/mean/proportion with units and denominator → 013/003 consumer trace. Test zero denominator and missingness without invented numbers. Inject join multiplication, hidden exclusion, semantic drift, unknown snapshot, and immature labels. |
| 008 | 009, 18-bootstrap | Question, typed Plan registration, exposure history | Validate all four named Plan profiles against 001-generated schemas at registration; missing directed fields and extra keys fail while lightweight exploration succeeds. Worker sees original question and revision/exposure timeline. Inject post-outcome plan change, declared resampling, unknown exposure, and worker restart. |
| 012 | 008, 009, 010 | Admission, budget, the single analytical identity predicate | Admit/reject proposal against graph, grant, evidence, and aggregate allowance. Inject no-progress loop, duplicate attempt, nested over-reservation, deadline, missing telemetry, and replacement worker without new budget. |
| 013 | 007, 008, 010, 18-bootstrap | Predictive Python calculator and TS facade consuming the 001 contract/lock | Python computes comparison under frozen request → TypeScript verifies exact binding/checks → 003 answer path. Inject leakage, target immaturity, evaluation tuning, metric/delta mismatch, valid null and low-power interval. |
| 003 | 002, 007, 008, 012, 013 | Worker orchestration, controller, public analytics CLI and extension registry | Real user ask → admission, worker, Python compute, check, finalization → answer and trace. Test typed command dispatch, dependency ordering, duplicate/cycle rejection, exact missing-capability handling and failed required hooks. Inject ambiguity, budget stop, six terminal outcomes, replacement session exposure continuity, failed required check and interrupted cross-scope finalization. |
| 019 | 003 | Artifact catalog/import/retention/SQLite FTS5 adapter | Register generated artifact or import 99 synthetic files → access-filtered catalog/search → viewer. Inject duplicate bytes at different paths, restricted sheet/title, corrupt index, catalog crash, 101st file/limit, and unsafe cleanup. |
| 023 | 003, 019, 012 | Append-only work ledger using 012 identity predicate | Admit comparison → produce readable ledger → expose assessment to 020 orientation. Inject omitted prose, null/failure, retry within one attempt, exact/related/incompatible/unresolved identities, redaction, and index loss. |
| 020 | 019, 023, 011 | Answer cards, query relevance/ranking, retrieval exposure | Finalized card → access-filtered 150-card search → automatic recall in later 003 inquiry and viewer. Inject empty/no-match queries, wrong cohort/period, prior-outcome exposure, summary omission, and restricted results. |
| 014 | 003, 019 | Source discovery and candidate source skill | Bounded source fixture discovery → candidate assertions/recipes → visible provenance. Inject malicious metadata, sensitive metadata, missing definition, and overprivileged/unbounded recipe. |
| 015 | 014 | One source-skill compatibility predicate and refresh | Later worker receives exact compatible version and runs its recipe. Inject same-schema unit/key/role/freshness drift, unused-column addition, and revoked or not-executed recipe. |
| 004 | 003, 014, 015 | First/second live inquiry source-memory integration | Cold discovery inquiry → exact source-skill candidate/validation → warm second inquiry and answer trace. Inject drift or grant reduction without replacing historical pin. |
| 016 | 003, 015 | Window identity, period comparison, invalidation traversal | New/corrected fixture window → traverse dependent records → 005/006 consumer and viewer. Inject two successive corrections, processing/event-time shift, missing metadata, and undeclared repeated look. |
| 017 | 003, 016, 010 | Use Assessment, review, Decision pointer | Claim/result/check/policy → requested use → configured authority and visible assessment. Inject changed policy/evidence, empty rationale, revoked authority, conflicting evidence, and internal save without publication request. |
| 005 | 004, 016, 017 | Integrated recurrence and use path | Live inquiry on a new window → compare/invalidate/reassess → inspect prior decision and current use state. Inject late correction and stale authorization; prior records remain. |
| 006 | 003, 016, 007, 010 | Inferential Python calculator and TS facade consuming the 001 contract | Live two-group rate-difference inquiry → exact Python result/check/trace. Inject repeated units, missing groups, small sample, nonrepresentative data, repeated look, and causal overreach. |
| 021 | 004, 020 | Bounded signal capture and candidate creation | Feedback/repeated validated procedure → candidate with scope/evidence → locked 48-signal evaluator. Inject answer-only praise, unvalidated result, duplicate signal, holdout outcome, and memory budget limit. |
| 022 | 004, 020, 021, 015 | Independent validation/authority, activation and later use | Candidate → independent test/owner decision → scoped activation → later live inquiry. Inject self-test-only candidate, business-rule conflict, revoked access, mismatched scope, and supplied-but-unexecuted recipe. |
| 018 | 002, 003, 004, 005, 006, 020, 022, 18-bootstrap | Frozen evaluator, metric registry and release decision | Score complete live question and evidence, metrics with denominators, key isolation and false-block plus fault recall. Seed missed threshold, missing denominator, leaked answer, post-batch threshold and unfair harness comparison. |
| 99 | 018, 005, 006, 020, 022 | Final acceptance test, completion report and Quickstart | Run declared release checks; execute real ask, trace, recall, source-skill reuse, and failed-check paths; report any unclosed graph boundary with evidence. |

No integration node substitutes its own implementation for the focused node listed in the ownership column. Feature 018 scores contributed cases but is not their primary owner. The live demo and browser routes are required consumers, not documentation-only promises.

## User acceptance paths

These are the real user-facing command actions frozen in [interfaces.md](interfaces.md). Acceptance uses synthetic/local fixture paths only and drives the same controller and viewer routes users will use.

| User action | Command | Observable completion |
| --- | --- | --- |
| Ask a bounded synthetic question | `dag analytics ask --project <path> --question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh` | Controller records question, principal/grant, budget, exact source and prompt/context digests; live worker proposes work; computation and evidence references are committed; terminal inquiry state is returned. |
| Open the answer card | `dag analytics answer show --inquiry <id> --open` | Answer card shows question, scoped answer/non-answer, population/period, uncertainty, limitations, state, current validity, and trace links in browser. |
| Trace a claim | `dag analytics trace --claim <id>` | Assertion resolves through exact Result selector, current Check Results, execution/attempt, transformation, source version, Plan/code/environment pins; restricted values are not returned. |
| Search past answers | `dag analytics answers search --query <text> --period <range>` | Accessible cards are filtered before ranking, carry scope/mismatch reasons, and produce explicit `no_match` when none is a direct answer. |
| Read the work timeline | `dag analytics ledger show --inquiry <id>` | Shows successful, null, failed, inconclusive, stopped, incomplete, and deliberate rerun records with rationale and exact refs. |
| Learn and reuse a source | `dag analytics source discover --source <fixture>` then the `ask` command above for a second question | First inquiry creates a scoped candidate with tested recipe; second worker receives its exact compatible version, records supplied refs and executed recipe, and shows reuse in its answer trace. |

The live path records `gpt-6-luna` and `xhigh`, plus the exact harness and version, model parameters, prompt/context and source digests, run/inquiry/attempt IDs, remaining/consumed budget, and all produced artifact IDs. No demonstration uses real confidential or remote source data.

## Evaluator isolation and locked metrics

Node 18-bootstrap owns versioned synthetic fixture inputs, the fixture lock, grant profiles, and the base evaluator interface. The worker/context compiler receives the question, permitted local data/source scope, policies, and task outputs only. It does not receive reference answers, relevance labels, or planted-fault descriptions. The independent evaluator runs after the worker response through a separate interface. Tests compare the actual worker packet/context digest to the fixture key/fault files and require zero key or fault-description bytes in context. Because the local shell may read checkout files, the profile is labeled `workflow-withheld; access isolation not enforced`; it is not described as a protected holdout.

Feature 018 owns evaluator execution/release calculations; feature owners own their named cases. The initial tabular registry remains exactly as approved in feature 018:

| Metric | Locked gate and minimum denominator |
| --- | --- |
| Answer quality | At least 0.90 on at least 20 questions |
| Unsupported promotion | Exactly 0 on at least 20 presented assertions |
| Fault recall | At least 0.95 on at least 20 planted faults |
| False-block rate | At most 0.05 on at least 20 valid claims |
| Trace completeness | Exactly 1.00 on at least 20 displayed assertions |
| Budget completion | At least 0.90 on at least 20 questions |
| Answer retrieval Hit@3 | At least 0.90: at least 18 primary cards in top three over 20 frozen queries and 150 cards |
| Ledger completeness | Exactly 1.00 on at least 20 admitted attempts |
| Candidate precision | At least 0.80 on at least 10 proposals in the frozen 48-signal capture fixture |
| Candidate recall | At least 0.80 on all 32 positive signals in the frozen fixture |

The five no-match queries, including an empty corpus, require zero false direct answers. Access leaks, invalid key isolation, unapproved threshold changes, missing/zero required denominators, or evaluation keys changing after batch start fail closed. Cost is reported as observed or unavailable and is not a gate until observable/enforceable. Fault recall is always reported with false-block rate. A passing 20-case batch establishes eligibility only for the named frozen fixture profile; it does not establish broad statistical reliability.

## Slice order and release commands

Use graph dependencies and the [Spec Kit milestone order](../../specs/README.md). The execution path proceeds from isolation/fixture ownership, evidence publication and checks, safe trace and fixed-evidence integration, data and Plan/exposure records, bounded admission and predictive computation, live inquiry, project artifact/ledger/card memory, source discovery/reuse, recurrence/use and inference, candidate activation, whole-product evaluator, and final convergence. The full node dependency and failure register above prevents a consumer or failure path from being skipped.

The planned final release checks remain:

```powershell
pnpm build
pnpm test
uv run --project python pytest
python scripts/audit_spec_coverage.py
pnpm smoke:viewer
```

Then run the locked whole-product evaluator and documented live synthetic `gpt-6-luna`/`xhigh` ask, trace, recall, source-skill reuse, and retained failed-check paths. Record each command's actual exit, counts, metric denominator, artifacts, and limitations in the feature verification report and final completion report. A missing or incompatible local neutral-package artifact, a missed threshold, or a missing observation is reported as blocked/failed with evidence; no feature acceptance criterion, policy, evaluator answer, or threshold is relaxed to obtain a pass.

### Node 00 document checks

Before any implementation node starts, review these three outputs against graph ownership and verify their placeholder scan and exact T-case table. Run the structural contract audit:

```powershell
python scripts/audit_spec_coverage.py
```

This script checks the existing specs/maps and does not run implementation fixtures. Node 00 does not run the DAG, change `dag.run.json`, alter graph ownership, or make an analytical/product action.


## Corrected implementation order — 2026-09-29

After 003/019/020 and source discovery/reuse 014/015, build candidate capture 021 and production activation 022 before the 004 live source-memory demonstration. Feature 004 depends explicitly on 022. Source-only fixture activation is not a substitute for that production path. Feature 008 acceptance uses its production facade and the available 009 stage coordinator; 003 owns subsequent delegated-controller integration assertions. The compiler topologically orders these prerequisites and continues to reject cycles and conflicting file owners. Product acceptance remains unchanged.

Primary T39 ownership is 020 per the canonical traceability audit. Feature 019 provides supporting restricted-workbook denial coverage for its own access boundary; a proposal trace or milestone summary listing T39 is not an exclusive ownership assignment.

Coverage-register amendment (2026-10-11): current pinned specifications contain 234 FRs, 219 SCs and 115 ordered edge cases. The final convergence matrix contains 637 rows including 61 T cases and eight paths. Earlier verification reports retain their historical counts and do not certify this revised register.
