# Node 00 — bounded architecture and interface plan

**Scope:** document the shared implementation architecture before feature nodes
implement product behavior. This stage writes only `docs/build/00-plan.md`.
Node 00 then produces `docs/build/ARCHITECTURE.md`,
`docs/build/interfaces.md`, and `docs/build/acceptance-plan.md`. It does not
change a feature spec, quality checklist, evaluator answer, release threshold,
runner manifest, or product code.

## Product contract and inputs

Treat the following as read-only contracts while planning:

- `AGENTS.md`, `docs/specs/advanced-analytics-dag-proposal.md` revision 2.6,
  `.specify/memory/constitution.md` version 1.4.1,
  `docs/specs/state-vocabularies.md`, and
  `docs/specs/access-and-judgment-contract.md`.
- All 23 `specs/*/spec.md` files and their existing
  `specs/*/checklists/requirements.md` quality checklists.
- `docs/specs/fr-verification-map.md`,
  `docs/specs/edge-verification-map.md`,
  `docs/specs/traceability-audit.md`, `specs/README.md`, and
  `scripts/audit_spec_coverage.py`.
- `docs/build/graph.json`, `docs/build/run-manifest.json`, and
  `docs/build/contract-lock.json` for task ownership, dependencies, and build
  orchestration.
- The inherited execution boundary in `src/runner.ts`, `src/store.ts`,
  `src/graph.ts`, `src/harnesses.ts`, `src/commands/run.ts`, and
  `src/factory/controller.ts`; `src/server.ts` and `viewer/run.html` are
  migration context only.

The proposal and feature specs govern analytical meaning. Process success,
claim support, evidence availability, and authorization remain separate.
Null, negative, inconclusive, conflicting, failed, and incomplete outcomes
are retained. Software build retries/reviews may be used by the orchestrator
for this build, but never define analytics outcomes or trigger analysis until
it finds a favorable result.

## Architecture decisions to freeze

### Runtime and authoritative computation

- Use TypeScript for the analytics controller, CLI, store, policy/access
  enforcement, APIs, and viewer. Keep analytics modules under
  `src/analytics/` and preserve the existing CLI only as the temporary neutral
  execution surface during extraction.
- Add a uv-locked package under `python/` for authoritative tabular
  computation. Use mode-specific Pydantic contracts for descriptive,
  predictive, and inferential requests/results. Python computes values;
  TypeScript admission, evidence binding, policy, and presentation checks
  decide whether those values can be committed or shown as supported.
- Freeze one portable versioned request/result contract and shared conformance
  fixtures. Do not maintain independent TypeScript and Python interpretations
  of the same wire schema. Record program, Python, dependency-lock, seed, and
  input versions with every authoritative computation.

### One neutral execution package

Node 001 extracts the authoritative `@dag-orchestrator/neutral-execution`
source into `packages/neutral-execution/` in this repository. It owns neutral
scheduling, processes, logs, locks, timeouts, artifact paths, and recovery.
Analytics consumes the exact-version workspace package; node 001 produces a
local release archive and actual digest, plus a tested legacy-software adapter
and shared conformance suite. Remote publication is not a build prerequisite.
The original software product remains on its frozen legacy version until it
adopts that artifact. Record that migration status honestly; do not claim an
upstream repository change occurred or maintain a second edited runner copy.

The current inherited runner is a migration source, not a second owner. Node
001 replaces the analytics checkout's independently edited runner copy with
the package adapter only after the shared contract and compatibility path
exist. The analytics adapter allows one process attempt, sets task retries, review
redo and repair rounds to zero, uses an empty fallback chain and disables final review; a classified infrastructure retry is a new
execution under the same analytical attempt and budget. Analytics controller
admission stays separate from the neutral scheduler. No second scheduler or
worker pool is introduced. Node 001 owns creating and testing the local package
and compatibility artifact; later nodes consume it without duplicating its
source. The compatibility test remains a blocking requirement.

Compatibility tests cover dependency ordering, command arguments and exit
status, logs, timeout/cancellation behavior, lock arbitration, artifact
paths, crash recovery, and replay. They establish neutral execution
compatibility only, not analytical support. The prior check of the original
software product is against the same package artifact and test vectors, not a
separately patched runner copy.

### Evidence, storage, and access

- Run-scoped analytics records live under `<run>.d/analytics/`; project-wide
  source skills and memory live under `.dag-analytics/`. Do not change neutral
  run serialization merely to store analytics state.
- Use immutable, versioned records and append-only commit/event history. A
  controller is the sole logical writer. Stage outputs in execution-specific
  directories; validate schema, exact identity, permissions, paths, and
  digests; commit against an expected manifest revision and commit ID; expose
  only committed revisions. Same-ID replay returns the prior commit; a
  competing expected-revision write returns a conflict without overwrite.
  Recovery trusts committed manifest references, reconciles idempotently,
  and leaves failed, orphaned, or partial output unpromoted.
- Every evidence reference identifies record ID/revision and content digest,
  not a filename alone. Bind a result to the exact Plan revision, code and
  environment lock digests, source/data version manifest, transformation
  versions, execution/attempt, output digests, and applicable check subjects.
  A changed binding makes the old check insufficient. Keep byte integrity
  distinct from the declared canonical-value or numerical-tolerance
  reproduction comparison.
- Use project-local SQLite with FTS5 as the first lexical index adapter.
  Committed manifests, imported-file records, ledger rationale, and durable
  curation/skill events remain authoritative. Rebuild the index from those
  inputs with stable IDs, access scope, availability, and usage links; index
  loss cannot create, erase, or promote an analytical record. Do not put raw
  sensitive rows in searchable summaries by default.
- Resolve each request to an authenticated local principal and configured
  roles. Enforce grants at controller context construction, source adapter,
  catalog filtering before ranking, and every viewer metadata/byte request.
  Missing grants deny access. Recheck current access on retrieval; record
  exposure and preserve an access-safe historical pointer after revocation
  or redaction. Label shell-bypassable source/write boundaries as workflow
  controls, not filesystem isolation.
- Serve only declared artifacts beneath approved roots. Escape untrusted
  report/source text, reject traversal and symlink escape, apply access before
  returning metadata or bytes, and show broken or restricted links without
  leaking the denied path or value.

### Admission, budgets, and incremental record kernel

Workers propose work and candidate outputs. The TypeScript controller checks
principal/grant, graph revision and acyclicity, dependencies, exact input
references, output schema/path declarations, frozen required policy, and
remaining aggregate reservations before admitting work. Semantic relevance
and reviewer judgments retain their named worker/reviewer attribution;
deterministic validation checks bindings, vocabulary, grants, and limits but
does not claim to prove analytical meaning.

Freeze the initial fixture allowance from 012: 15 minutes of admitted work,
12 worker invocations, four optional branches, three Plan revisions, and a
five-minute command timeout. Reserve room for required checks and final
reporting. Do not advertise hard wall-time cancellation until tested on the
host; otherwise call it a scheduling cutoff. Enforce task/branch counts and
shared CPU/worker/source-query reservations where the adapter supports them.
Label unobservable token, monetary, memory, and source-byte limits as
estimates or unavailable. Reject work that depends on an unenforceable hard
limit.

Add records with their first production caller and first real consumer in the
same earliest feature slice; do not create an unused generic ledger or
record-model phase. The minimum kernel is introduced in owning slices:

| Record/capability | First owner | First production consumer |
| --- | --- | --- |
| Principal and grant | 001 | Worker context, source, catalog, and viewer checks |
| Computation, output, evidence commit | 009 | Publisher/recovery and then fixed-analysis checks |
| Frozen policy and Check Result | 010 | Claim eligibility and 011 trace |
| Report assertion and evidence trace | 011 | Browser viewer and fixed-analysis path 002 |
| Source/data and transformation versions | 007 | Predictive profile 013 and inquiry 003 |
| Question, Plan revision, exposure | 008 | Admission 012 and inquiry 003 |
| Budget, proposal admission, analytical identity core | 012 | No-progress admission; ledger 023 later reuses the predicate |
| Descriptive calculator/result | 007 | Directed/exploratory summaries in inquiry 003 |
| Predictive request/result | 013 | Typed TypeScript façade and inquiry 003 |
| Artifact identity/catalog index | 019 | Ledger 023 and answer search 020 |
| Attempt ledger and match assessment | 023 | Answer orientation 020 |
| Answer card, retrieval, exposure | 020 | Later inquiry orientation and skill capture/activation |
| Source skill and compatibility | 014 / 015 | Second inquiry 004 and recurrence 016 |
| Window/comparability/invalidation | 016 | Use integration 005 and inferential profile 006 |
| Use Assessment and Decision | 017 | Recurrence/use integration 005 |
| Inferential request/result | 006 | Its end-to-end answer/trace path and evaluator 018 |
| Skill candidate and activation event | 021 / 022 | Later skill use in 022 and inquiry 004 |
| Reference evaluator and release assessment | 18-bootstrap / 018 | Feature tests as they land, then final convergence 99 |

Resolve the 012/023 dependency before implementing the full ledger: 012 owns
the one shared `src/analytics/analytical-identity.ts` predicate and its typed
identity dimensions. It serves 012's no-progress admission immediately.
Feature 023 consumes that function, owns durable attempt entries and
canonical `exact`, `related`, `incompatible`, and `unresolved` match outcomes,
and adds rationale/provenance. It must not reimplement the identity
comparison. Any unknown required identity dimension is unresolved, never an
exact match.

## Frozen interfaces and feature-file ownership

Node 00 freezes interface names and versions in `docs/build/interfaces.md`;
feature nodes implement only their graph-owned facades and use those
contracts. The freeze must include `AnalyticsRequestV1`, the mode-specific
Pydantic plan/result contracts, `CheckResultV1`, `Principal`, `SourceGrant`,
`EvidenceRef`, `CommitRequestV1`, `CommitResultV1`, `AttemptIdentityV1`,
`AnalyticalMatchV1`, `AnswerCardV1`, `ArtifactRecordV1`, and versioned
manifest/event envelopes. The evidence store exposes `commitEvidence()` and
`recoverEvidence()`; access exposes `requireGrant()` and
`filterAccessible()`; the controller exposes `admitProposal()` and
`finalizeInquiry()`; the index exposes `rebuildIndex()` and
`searchAccessible()`; the Python boundary accepts and returns versioned JSON
through typed Pydantic models. These names, required fields, canonical states,
error/conflict outcomes, and schema versions are frozen before parallel
feature work.

`docs/build/graph.json` remains the file-level source of feature ownership.
The following graph outputs are separate facades; no feature may edit another
feature's facade to bypass its owner:

| Node | Owned implementation surface | First consumer/boundary |
| --- | --- | --- |
| 00 | `docs/build/ARCHITECTURE.md`, `docs/build/interfaces.md`, `docs/build/acceptance-plan.md` | All later nodes |
| 001 | `src/analytics/context.ts`, `principals.ts`, `runner-adapter.ts` | Context/access and neutral-engine boundary |
| 18-bootstrap | `src/analytics/evaluation/registry.ts`, `fixtures/`, `src/analytics/acceptance/18-bootstrap.test.ts`, `docs/build/evaluation-fixture-lock.json` | Frozen inputs/evaluator fixture API |
| 009 | `src/analytics/store.ts`, `publication.ts`, `stages.ts` | Durable evidence publication/recovery |
| 010 | `src/analytics/checks.ts`, `policies.ts` | Structured checks and supported-presentation eligibility |
| 011 | `src/analytics/report.ts`, `http.ts`, `viewer/analytics.html` | Safe user answer and trace |
| 002 | `src/analytics/demo-evidence.ts`, `python/analytics_worker/demo_evidence.py` | Integrated evidence path and failure/recovery demo |
| 007 | `src/analytics/data.ts`, `measurement.ts`, `descriptive.ts`, `python/analytics_worker/descriptive.py` | Source, grain, denominator, and transformation contract |
| 008 | `src/analytics/plans.ts`, `exposure.ts` | Locked plans and exposure history |
| 012 | `src/analytics/inquiry.ts`, `budget.ts`, `analytical-identity.ts` | Admission, aggregate allowance, shared identity predicate |
| 013 | `python/pyproject.toml`, `python/uv.lock`, `python/analytics_worker/predictive.py`, `src/analytics/predictive.ts` | Authoritative predictive computation and typed TS facade |
| 003 | `src/analytics/worker.ts`, `controller.ts`, `cli.ts`, `extensions.ts` | First live question-to-answer user path |
| 019 | `src/analytics/catalog.ts`, `import.ts`, `retention.ts` | Artifact inventory and rebuildable index |
| 023 | `src/analytics/ledger.ts` | Durable attempt projection using 012 identity |
| 020 | `src/analytics/answers.ts`, `retrieval.ts` | Cards, ranking, and automatic recall |
| 014 | `src/analytics/source-discovery.ts`, `source-skills.ts` | Scoped source skill candidate |
| 015 | `src/analytics/skill-compatibility.ts`, `skill-refresh.ts` | Single source-skill compatibility predicate and refresh |
| 004 | `src/analytics/demo-source-memory.ts` | Two-inquiry source-skill reuse path |
| 016 | `src/analytics/recurrence.ts`, `invalidation.ts` | Window comparison and dependency invalidation |
| 017 | `src/analytics/authorization.ts`, `decisions.ts` | Use authorization and Decision pointer |
| 005 | `src/analytics/demo-recurrence.ts` | Integrated recurrence and consequential-use path |
| 006 | `python/analytics_worker/inferential.py`, `src/analytics/inferential.ts` | Scoped two-group inferential profile |
| 021 | `src/analytics/skill-capture.ts` | Bounded capture signals and candidates |
| 022 | `src/analytics/skill-activation.ts` | Independent validation/authority and later use |
| 018 | `src/analytics/evaluation/evaluator.ts`, `release.ts` | Frozen whole-product evaluation and gate |
| 99 | `src/analytics/acceptance/99.test.ts`, `docs/build/COMPLETION.md`, `docs/QUICKSTART.md` | Final merged-path convergence and handover |

Shared-file rules: node 001 owns `python/pyproject.toml` and
`python/uv.lock`, the strict common/mode-specific Pydantic contracts, and
generated schemas. This bootstrap runs before 008 plan registration; 013
implements computation against the existing contracts. Node 006 adds only
its inferential module and TS façade and
requests any lockfile change from the owner. Node 18-bootstrap owns the
fixture registry/lock and base evaluator API; feature owners contribute
feature-scoped cases without changing frozen expected answers or thresholds.
Node 018 owns evaluator/release implementation, not each domain feature's
primary acceptance case. Each feature owns its named `src/analytics/acceptance/<id>.test.ts`
and its own three Spec Kit artifacts. Nodes 002/003/004/005 exercise their
integrated paths but do not take over focused-feature interfaces or T-case
ownership. No shared implementation file has two owners. The graph explicitly assigns the neutral source/archive/workspace and contract conformance files to 001; descriptive.py/descriptive.ts to 007; and extensions.ts to 003. Node 009 owns both run and project publication/CAS/recovery and the production single-stage coordinator used by 002 before the full inquiry controller 003 exists. Node 002 owns its fixed synthetic Python calculation fixture; the general descriptive calculator remains owned by 007. The named PlanRegistrationV1 models, project-memory commit protocol, and fixed CLI/lifecycle provider seam are frozen in interfaces.md. Later domain features register their providers from their own files without editing the shared controller. Node 003 coordinates durable cross-scope finalization and resumes interrupted steps without claiming a cross-store transaction.

## Acceptance strategy and corrected T-case ownership

`docs/build/acceptance-plan.md` records the existing one-primary-check-per-FR
map (234 FRs), all 114 mapped edge cases, all 61 proposal cases, and an
end-to-end producer → controller → viewer check for each graph slice. It
references each feature's accepted FRs/SCs and requirement checklist without
copying or weakening them. `python scripts/audit_spec_coverage.py` remains the
structural map audit; passing it is not evidence that implementation fixtures
passed. Feature tests and synthetic failure demos must report their actual
case IDs and observed outcomes.

The prior rejection is resolved by using exact focused-feature ownership from
`docs/specs/traceability-audit.md`, not inclusive ranges. In particular:

- Feature 019 owns T28–T30, **T37**, and T43–T47.
- Feature 020 owns T31, T35, T36, T39–T42, T56, and T57.
- Feature 021 owns T32 and **T38**.
- Feature 022 owns T33, T34, and T59.

Thus 020 must not claim “T35–T42” as a primary range: that would duplicate
T37 and T38. It can exercise those cross-slice behaviors as supporting
integration evidence while 019 remains the primary owner of T37 and 021 the
primary owner of T38. The acceptance plan's T-case owner column must match
the ownership audit exactly; feature 018 scores frozen cases but does not
become their primary domain owner.

The acceptance plan and feature verification reports preserve the locked
metrics: for example, retrieval Hit@3 at least 0.90 on 20 queries over 150
cards; the five no-match queries with zero false direct answers; capture
precision and recall at least 0.80 on the locked 48-signal fixture; and all
feature 018 release thresholds and minimum denominators. Reference answers,
labels, and planted-fault definitions stay outside worker context. No threshold
or reference answer is edited to make an implementation pass.

Use synthetic/local fixtures only. The live worker path records
`gpt-6-luna` with `xhigh` reasoning, harness/version, budgets, source and
prompt digests, and the generated artifact IDs. The local evaluation profile
is explicitly workflow-withheld when the worker can read local files; it is
not described as an enforced holdout. The independent evaluator executes
after the worker response and reads answer keys from a separate evaluator
interface unavailable to the worker/context compiler. Access-leak tests must
show zero key/fault-description bytes in worker context.

Freeze real user command paths in `interfaces.md` and cover them in
acceptance. The planned paths are:

1. `dag analytics ask --project <path> --question <text> --source <fixture>`
   to start a bounded synthetic question through the controller.
2. `dag analytics answer show --inquiry <id>` to open the answer card and
   browser view.
3. `dag analytics trace --claim <id>` to inspect the report assertion through
   exact result, checks, execution, transformation, and source version.
4. `dag analytics answers search --query <text> --period <range>` to verify
   access-filtered retrieval and no-match behavior.
5. `dag analytics ledger show --inquiry <id>` to inspect null, failed,
   incomplete, and intentional rerun events.
6. `dag analytics source discover --source <fixture>` followed by a second
   `dag analytics ask ...` to demonstrate exact source-skill reuse.

The interface document may settle flag ordering and output formatting, but
these user actions and their controller/viewer path must remain available.

## Bounded sequence, verification, and failure demonstrations

| Step | Graph nodes and owned files | Required verification/failure evidence |
| --- | --- | --- |
| 1. Freeze architecture | 00: three docs listed above | Graph node/dependency/file ownership audit; exact T ownership audit; unresolved-placeholder scan; `python scripts/audit_spec_coverage.py` |
| 2. Boundary and fixture base | 001, 18-bootstrap | Neutral conformance in original and analytics products; principal/grant deny/allow; zero factory context; T53; evaluator-key context exclusion |
| 3. Evidence slice A | 009, 010, 011, then 002 | Pre/post-commit crash, lost acknowledgement replay, conflicting CAS writers, changed/missing outputs, stale/omitted/malformed checks, overlap fail, valid negative, HTML/path traversal, viewer permission denial; T01–T03, T10–T15, T22–T23, T52, T60 |
| 4. Delegated slice B | 007, 008, 012, 013, then 003 | Grain/denominator and temporal leakage faults; plan revision/exposure and replacement-worker continuity; exact limits, no-progress, nested reservations, unknown cost, wrong result binding; valid/null/low-power model comparisons; T04–T09, T12, T24, T26–T27, T61 |
| 5. Memory slice B2 | 019, 023, then 020 | 99-file import/duplicates/bounds, missing provenance, catalog loss/rebuild, safe cleanup/export; attempt with absent prose, exact/related/incompatible/unresolved match, no duplicate infrastructure attempt; automatic recall, wrong cohort, empty/no-match, access-filter-before-ranking and pinned 150-card score; T28–T31, T35–T37, T39–T42, T43–T51, T56–T57 |
| 6. Source memory C | 014, 015, then 004 | Inert malicious metadata, bounded read recipe, no secret/raw-row skill, exact supplied and executed version, role/unit/meaning drift, stale/revoked access; two live inquiries use same compatible fixture skill; T17–T19, T25 |
| 7. Recurrence and use D | 016, 017, then 005 | New/corrected windows, comparable/qualified/incomparable/unassessed states, complete dependency invalidation, repeated-look exposure, empty-rationale and stale/revoked authorization; routine uncertain answer remains possible; T16, T20–T23, T58 |
| 8. Method and skill paths | 006, 021, 022 | Valid and invalid dependent inference, subgroup exposure, no sensitive inference from names, candidate deduplication/precision/recall, no self-activation, explicit owner approval and later scoped reuse; T32–T34, T38, T54–T55, T59 |
| 9. Whole-product release | 018 then 99 | Frozen evaluator, denominators/thresholds, key isolation, false-block plus fault recall, live held-out synthetic question, full release commands and browser smoke; no threshold tuning on evaluated answers |

At each step use the feature-owned verification fixture and live production
caller; an isolated unit test cannot close a graph boundary. Integration nodes
exercise the focused producer and its consumer/viewer, including one injected
failure, without duplicating the owner's implementation. All task files in
`docs/build/graph.json` retain their stated dependencies. Node 00 does not
change orchestration artifacts, invoke `dag`, or perform any product action.

### Release and verification commands

Planning artifacts are checked with:

```powershell
python scripts/audit_spec_coverage.py
```

Review the three generated documents against every graph node and output,
confirm they contain no template residue, and verify the focused T-case owner
table against `traceability-audit.md`.

After implementation, final convergence uses the graph's release commands:

```powershell
pnpm build
pnpm test
uv run --project python pytest
python scripts/audit_spec_coverage.py
pnpm smoke:viewer
```

Then run the frozen whole-product evaluator and documented live synthetic
`gpt-6-luna`/`xhigh` ask, trace, recall, source-skill reuse, and failed-check
paths. The build orchestrator owns task execution and run-file lifecycle; this
plan stage does not edit `dag.run.json`, invoke the runner, change branches,
merge, push, or commit. No analytics result, policy threshold, reference
answer, or release status is changed by documentation planning.
