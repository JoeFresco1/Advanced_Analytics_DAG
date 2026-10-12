# Advanced Analytics DAG
## Product vision and implementation proposal

**Revision:** 2.6 — access and judgment contracts, state transitions, and corrected predictive classification

**Date:** 2026-09-27

**Status:** Proposed behavior and delivery contract; analytics capabilities described here are not yet implemented.

**Repository:** advanced-analytics-dag

## 1. The product we are building

Give the worker a question and access to data. It should understand the source, turn the question into an appropriate analysis, write and execute the necessary code, challenge its results, and return a useful answer with inspectable evidence. On a later inquiry, it should reuse what it has learned about the source and recognize what has changed.

The user might ask “Does X help predict Y?”, “Why did this metric move?”, “What differs across these groups?”, or simply “What is going on in this dataset?” The worker performs the analytical labor. The user should not need to construct a DAG, write YAML, select every statistical test, or run the code.

The answer should explain what was found, the population and period it describes, how large the effect or predictive improvement is, what uncertainty remains, and what the evidence permits us to conclude. “There is not enough evidence to answer that” is a legitimate answer when accompanied by a precise explanation and a useful next step.

The central rule is:

> Execution records what happened. Evidence establishes what the work supports. A decision records what someone chose to do with it.

Successful execution, valid measurement, reproducibility, evidential support, reviewer approval, and authorization for a particular use are distinct facts. No single green badge substitutes for them.

The DAG provides scheduling and recovery underneath this experience. The product people use is an analytical worker with a durable memory of questions, evidence, and sources.

## 2. Product commitments and initial scope

| Commitment | Consequence |
| --- | --- |
| The worker does the work | It authors plans, contracts, scripts, checks, explanations, and proposed follow-up questions. |
| Routine work proceeds autonomously | Ask only when missing meaning, access, or a consequential choice materially changes the answer and cannot be resolved from available evidence. |
| Answers are inspectable | A reported value resolves to a result, executable computation, transformation, and data version. |
| Uncertainty is preserved | Unsupported, inconclusive, conflicting, and negative outcomes remain visible. |
| Knowledge improves through reuse | Project memory preserves an analysis ledger, analytical results, and tested skills for sources, business rules, and methods. |
| One neutral engine serves separate products | Analytics lives in its own repository and depends on neutral execution primitives; software-factory code and prompts are absent from its worker context. |
| Tools are implementation components | Existing local or open-source tools may help; purchasing a platform or requiring a paid service is outside the plan. |
| Claims are bounded by actual guarantees | Workflow conventions, enforced isolation, observed facts, and inferred meanings are labeled distinctly. |

The first release targets tabular data, descriptive summaries, local Python execution, and one usable exploratory-to-predictive workflow. SQL ingestion and reusable source skills follow immediately through a second complete workflow. R, broader inferential profiles, causal designs, and distributed compute remain supported architectural directions, not five parallel platforms to implement before the first answer.

The initial user is a person delegating analytical work: an analyst, researcher, or domain expert. They may know the business question without knowing the tables or method. The worker must retain the original question, show its operational interpretation, and expose consequential assumptions.

When ambiguity is manageable, the worker can analyze explicit alternatives. When a term such as “active customer” materially changes the denominator, it must resolve or clearly preserve that ambiguity. It must not silently invent an authoritative definition.

## 3. Non-negotiable invariants

These rules determine whether an implementation meets the proposal.

| ID | Invariant |
| --- | --- |
| I01 | Every analytical computation is bound to an exact plan revision, code digest, environment, and input version manifest; agent authoring invocations retain their own context and output records. |
| I02 | Every reported numerical assertion has a result binding; missing or altered evidence prevents it being presented as supported. |
| I03 | A check applies to exact subject artifacts and a recorded validator/policy version. Editing an artifact makes its old check insufficient. |
| I04 | The actual check outcome is distinct from whether its command executed successfully. A valid negative analytical result does not trigger retries until a positive result appears. |
| I05 | A substantive plan revision never erases earlier work or prior outcome exposure. Human approval cannot change historical exposure into pre-specification. |
| I06 | A worker cannot relax a required check or release policy. An authorized policy change creates a new version and requires re-evaluation without erasing the original failure. |
| I07 | Dataset grain, denominator changes, joins, time boundaries, exclusions, and transformations remain attributable. |
| I08 | Missing information is explicit: unknown exposure, unavailable usage, missing evidence, and unperformed reproduction never become “none,” zero, or pass. |
| I09 | A claim's interpretation and permitted use are evaluated separately. A change in intended use triggers a fresh policy evaluation. |
| I10 | Every run and each discretionary branch has bounded time/work allowances and an explicit stopping reason. |
| I11 | Reusable source, method, and business-rule skills are scoped, versioned guidance. Their activation, use, validation, and invalidation are recorded. They do not grant privileges. |
| I12 | Domain state has one coordinated writer; recovery cannot publish a partly written result or attach an old check to a new execution. |
| I13 | Guarantees match the execution environment. A worker with unrestricted local access is not claimed to be isolated from local data, policies, or evidence files. |
| I14 | A feature is complete only when a real worker path produces it, the controller consumes it, and the user can inspect its effect. |
| I15 | Every committed output has a catalog identity, role, availability state, and provenance link or an explicit unknown-origin label. File names alone never determine importance. |
| I16 | Reusable skill candidates arise from recorded signals and validation. Activation records scope, authority, tests, and reason; a positive user reaction does not by itself certify a general business rule. |
| I17 | Result memory and skill instructions remain distinct. A later relevant inquiry considers compatible prior answers; retrieval preserves source, current validity, access scope, and any outcome exposure it introduces. |
| I18 | The project catalog is recoverable from durable evidence and curation records; losing a search index cannot erase the analytical history or silently orphan a claim. |
| I19 | Managed analytical outputs follow a declared count, size, role, and retention policy. Scratch tables do not become permanent exports by default; evidence needed for a claim is retained or its loss is made visible. |
| I20 | Every admitted analytical attempt has a recoverable, human-readable ledger entry linked to exact evidence. Later work checks prior attempts before repeating them and records why an exact or related analysis is rerun. |

## 4. Worker behavior and the evolving inquiry

The worker follows a bounded loop:

1. **Orient:** identify sources, retrieve relevant prior answers and compatible skills, inspect their validity and source meaning, and record the question's intended use.
2. **Frame:** define the population, unit of analysis, target measure or estimand, time window, and necessary assumptions. Choose the analytical approach.
3. **Explore if needed:** perform bounded profiling and comparisons; record what was inspected and which questions the observations suggested.
4. **Commit a plan:** record the method, partitions, search space, comparisons, stopping rule, required checks, and budget before directed execution.
5. **Execute:** create code and run computations using versioned inputs. Parallelize only tasks that do not change each other's context or evidence.
6. **Validate and challenge:** run required checks, examine alternative explanations, and resolve problems within the remaining budget.
7. **Answer:** produce scoped claims, charts, limitations, and evidence links. If the question remains unanswered, state why.
8. **Decide whether to continue:** propose a specific additional action only if it addresses a material unresolved issue and fits the budget.

The worker can propose additional tasks, but the controller validates and admits them. Proposals include the question addressed, expected output, estimated cost, dependencies, and why existing evidence is insufficient. Model-reported “information gain” is a scheduling suggestion, never proof that an action is worthwhile.

Each invocation receives a controller-generated context packet: question, plan, approved source references, applicable policies, prior results/rejections, exposure history, remaining budget, and exact requested outputs. Summaries point to original records. A restarted session or a different harness does not erase the inquiry's prior exposure or create an independent replication. Record the harness/model settings when available and the supplied context digest; the CLI continues to own its internal tool loop.

The inquiry stops when the requested question has an adequately supported answer, the available data cannot answer it, a material ambiguity requires the user, a budget is exhausted, or a failure prevents reliable completion. A settled task graph alone does not mean the question was answered. Conversely, an honest inconclusive answer can be complete without further work.

The controller records one inquiry outcome using the [canonical state vocabulary](state-vocabularies.md): `answered`, `answered_with_uncertainty`, `insufficient_evidence`, `needs_input`, `budget_exhausted`, or `execution_blocked`. A correctly measured but imprecise effect differs from a source that cannot support the question at all. Claims keep their own evidence states. The worker cannot choose `answered` merely because all commands exited zero.

The canonical vocabulary gives terminal-state precedence and separate plan,
Claim, predictive-comparison, period-comparison, and current-validity values.
The worker supplies semantic judgments with reasons and evidence IDs; the
controller checks bindings and policy without claiming it has proved meaning.
The [access and judgment contract](access-and-judgment-contract.md) defines
principal/grant enforcement and attribution across source, memory, and viewer.

The analytics entrypoint returns this outcome together with the evidence-completeness assessment. A valid completed inconclusive answer is successful work; an execution block or exhausted budget is reported as incomplete. Underlying DAG task exit codes cannot override that domain outcome. The controller validates required evidence mechanically and preserves the recorded methodological judgment; it does not claim to prove semantic correctness from task status.

The next-action loop has a maximum number of revisions and a no-progress check. Repeating the same analysis without new evidence, a changed justified method, or an authorized replication does not count as progress.

## 5. Execution graph and inquiry history

The execution graph answers “what must run before what?” Each admitted revision remains acyclic. Task dependencies are execution prerequisites, not evidence relationships.

The inquiry history answers “why was this done, what was learned, and what changed?” It links questions, plans, executions, results, claims, checks, and reviews. Supporting and conflicting results can coexist. A new cohort is not automatically a contradiction: comparability of population, metric, method, and period must be assessed first.

The first implementation needs five core records: **Question, Plan, Execution, Result, and Claim**. Dataset manifests, transformation manifests, Check Results, and Review Records are necessary supporting evidence. They can be ordinary versioned documents rather than separate services or a graph database.

A **work ledger** is a readable projection over these records, not a second source of truth. It answers “what did we try, what happened, and why did we continue or stop?” It includes incomplete, failed, null, and inconclusive attempts. The controller derives mechanical fields from committed records; worker interpretation is attributed and linked to its supporting Claim or explicitly marked provisional. Rebuilding the ledger index cannot rewrite the underlying history.

| Record | Required meaning |
| --- | --- |
| Question | Original text, operational interpretation, population/time scope, origin, intended use, unresolved definitions. |
| Plan | Immutable revision; method, target, comparisons, inputs, split/search/stopping rules, check policy, exposure context, budget. |
| Execution | Concrete invocation and attempt, plan revision, code/environment/input digests, timestamps, outcome, usage, worker/harness identity. |
| Result | Computed quantities with units, denominator, uncertainty method, scope, and exact artifact references. |
| Claim | Interpreted assertion, claim type, scope, supporting/challenging results, limitations, and review references. |
| Dataset manifest | Source identity, snapshot or window identity, schema/definition versions, content or source-native version, retrieval and access scope. |
| Transformation manifest | Inputs/outputs, code and parameters, grain, row/entity counts, exclusions, joins, and producing execution. |
| Check Result | Exact subjects, validator identity, policy version, observed values, applicability, outcome, evidence, and rationale. |
| Review Record | Subject revisions, reviewer identity/type, method, rationale, unresolved issues, and outcome. |
| Use Assessment | Requested use, applicable release policy, evidence revision set, authorization outcome, and any exception. |
| Decision, later | Action selected, deciding actor, supporting claims, constraints, time, and an external action reference. |

A simple study ID can join runs without building a separate study-management subsystem. A Decision is not another name for a Claim: accepting a business risk does not establish that a proposition is true.

Records receive stable IDs plus explicit revisions or content digests. An immutable record is superseded through a new record or relationship. Current status is a derived view over that history. Correcting a typo need not create a new statistical analysis, but the edit is still recorded; substantive changes to scope, method, evidence, or meaning do.

This organization is compatible with the entity/activity/agent distinction in [W3C PROV](https://www.w3.org/TR/prov-primer/). Full PROV serialization is optional; it is not an initial implementation dependency.

The following is the control flow; each execution DAG admitted by the controller remains acyclic:

~~~mermaid
flowchart LR
  Q[User question] --> C[Analytics controller]
  P[Evidence and use policies] --> C
  S[Versioned source skills] --> C
  C --> R[Existing DAG runner]
  R --> W[Worker and computations]
  W --> O[Candidate outputs]
  O --> C
  C --> E[Committed inquiry and evidence]
  E --> V[Answer and trace viewer]
~~~

## 6. Data identity and measurement come before method

A schema and row count do not establish that two analyses used the same data. Every acquisition must describe what was read and how consistent that read was.

| Source | Minimum acquisition record |
| --- | --- |
| Local file | Source identity, immutable copy or protected version reference, byte digest, parsing options, schema, and acquisition time. |
| Database | Connection identity without secrets, role/scope, query and parameter digest, snapshot/as-of semantics, isolation guarantee, extraction time, and materialized output or retrievable native version. |
| API | Endpoint/version, query scope, pagination/completeness evidence, retrieval window, and materialized response identity. |
| Event stream | Topic/source identity, event and processing time definitions, offsets or equivalent source positions, window boundaries, watermark, lateness/deduplication rules, and materialized window identity. |

A query string plus a timestamp is insufficient to reproduce a changing database. Source adapters must state their consistency guarantee; for example, PostgreSQL's statement and transaction snapshot behavior depends on isolation level. The design must use the actual guarantee provided by the source, not a generic “SQL snapshot” label. See [PostgreSQL transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html).

When the exact source cannot be retained or retrieved, retain the permitted derived evidence and label replay availability accurately. Read-only access must be enforced by credentials or an execution boundary where promised; asking a worker to use SELECT is a convention.

Every transformation records:

- Observation grain and entity keys before and after.
- Row and distinct-entity counts, with reconciled exclusion reasons.
- Join keys, intended cardinality, unmatched rates, and actual multiplication.
- Missingness treatment, category definitions, units, currency/timezone, and time cutoffs.
- Code, parameters, environment, and input/output references.

An increased row count can be correct; an unexplained change is the problem. Never impose a universal “row counts must stay equal” check.

A stream snapshot must distinguish provisional windows from finalized windows. Late arrivals or corrections produce a new version and an explicit correction relationship. They cannot silently rewrite the evidence for an earlier answer.

## 7. Analytical methods and claim limits

Mode specifies evidence requirements. Intended use and actual evidence state govern review and authorization. The first release implements a small profile set with an honest “unsupported method” response elsewhere.

| Approach | Questions it can address | Required attention |
| --- | --- | --- |
| Exploration | What looks unusual or worth testing? | Search scope, observed outcomes, comparisons tried, sampling and missingness. |
| Description | How much, how often, for whom, and when? | Denominator, weighting, measurement, coverage, relevant uncertainty. |
| Inference | What estimate or test addresses this predeclared question? | Estimand, sampling/design, dependence, assumptions, multiplicity, uncertainty interpretation. |
| Prediction | How well does information available at prediction time predict unseen outcomes? | Baseline, split strategy, feature availability, preprocessing, tuning separation, metric and evaluation uncertainty. |
| Causal analysis | What intervention effect is identifiable under a stated design? | Intervention and estimand, identification assumptions, design credibility, sensitivity and competing explanations. |

Forecasting, survival analysis, experiments, anomaly detection, and optimization can become explicit profiles when a real use case justifies them. They must not be approximated silently by whichever generic test the worker knows.

Method policies must address the following errors:

- **Absence of evidence:** a non-significant estimate is not proof of no effect. Claims of equivalence or practical irrelevance require an appropriate design and a stated meaningful-effect range.
- **Selection:** record comparisons, subgroup searches, model trials, and outcome inspections. A predeclared search is allowed within its plan; selecting a favorable result after the fact changes the claim's evidential status.
- **Optional stopping:** repeated inspection needs a planned sequential procedure or an explicit exploratory interpretation. “Keep running until significant” is not a retry policy.
- **Predictive direction:** transform every candidate-versus-baseline metric into a predeclared favorable-direction gain before applying a practical threshold; a low-power null case remains inconclusive when its interval crosses that threshold.
- **Dependence:** repeated people, sites, households, time series, or overlapping label windows can invalidate row-wise independence and naive splits.
- **Leakage:** feature availability, target maturity, preprocessing fitted only on training folds, and tuning separation matter beyond disjoint row IDs.
- **Attribution:** a model that contains X and predicts Y does not establish that X adds predictive value. The claim requires a suitable comparison, such as a planned model with and without X on comparable evaluations.
- **Confounding and adjustment:** regression adjustment alone does not establish causality; mechanically adding covariates can change the estimand or introduce bias.
- **Group comparisons:** category definitions and missingness must be explicit. Do not infer gender from names or silently collapse categories. Small or poorly represented groups limit the claim.

Report effect size, uncertainty, assumptions, and practical relevance together. P-values do not measure the probability that a hypothesis is true or the importance of an effect; decisions should not depend only on crossing a threshold. See the [ASA statement](https://www.amstat.org/asa/files/pdfs/p-valuestatement.pdf).

For predictive work, transforms learned from data belong within the training procedure, including cross-validation. A pipeline can help, but using one does not certify every leakage risk. See [scikit-learn's leakage guidance](https://scikit-learn.org/stable/common_pitfalls.html).

Uncertainty intervals record their type, confidence/credibility level where applicable, method, sampling unit, and assumptions. A bootstrap is not an automatic repair for an invalid design. Model confidence scores or agreement between LLM reviewers are not statistical uncertainty estimates.

## 8. Plans, exposure, and revisions

A directed plan specifies the work before it is admitted: population, measurement definitions, estimand/target, method, baseline, comparisons, partition roles, search space, stopping rule, uncertainty calculation, required checks, and intended use. Fields depend on the implemented profile; exploration initially needs only purpose, source references, and output declarations.

The worker authors the contract. The controller validates structure and references at registration, then binds realized versions before execution. A locked plan can identify an acquisition recipe; the resulting dataset version must be bound before analysis starts. “Latest data” is not a stable execution input.

Separate code authoring from authoritative computation: the agent produces a candidate program; admission pins its code and dependencies; the computation executes that version. Exploratory computations inside an agent's tool loop still create exposure and must be captured if their outputs support a report. An opaque CLI transcript alone cannot satisfy computation provenance. If a harness cannot expose the needed events, use controller-managed computation tasks and treat unrecorded agent calculations as unsupported. Record interpreters, dependency locks, relevant library versions, seeds, and hardware/runtime details where they affect reproduction.

Lock time alone cannot prove pre-specification. Exposure records distinguish access to a schema, predictors, labels, aggregates, evaluation metrics, or prior claims. Record actor/session, source/partition/version, what was exposed, when, and whether the record is observed by the system or self-reported. Unknown prior knowledge remains unknown.

Pre-specification is relative to a named question/comparison and evidence boundary. A plan informed by development data can be frozen before a new evaluation, with both its exploratory origin and its evaluation lock shown. Do not collapse those facts into a global “pre-registered” label or treat ordinary development work as invalidating every possible future confirmation.

A new seed within a predeclared resampling scheme or a model inside a locked search space is an ordinary planned execution. Changing the method, population, split, search space, or stopping rule in response to results creates a new plan revision. Correcting a code bug also creates a new execution and code digest; record whether it changes the analytical plan and which outcomes had already been seen.

Post-observation status is historical fact. Review may authorize exploratory use, identify valid selective-inference methods, or request a new confirmatory study on suitable data. It cannot retroactively make the old analysis pre-specified. A new valid confirmation is a new record linked to the exploratory origin.

There are two evaluation access profiles:

- **Local workflow withholding:** the controller delays presenting evaluation outputs, but an unrestricted local worker may still read files. Label this “workflow-withheld; access isolation not enforced.” No recorded access is not proof of no access.
- **Enforced evaluation boundary:** a separate security identity or service holds data the worker cannot access, runs an approved frozen evaluation, controls output release, and records evaluation requests. Limiting label access without limiting repeated metric queries is insufficient.

The local profile is the first release. The stronger profile requires adversarial access tests before the product advertises that guarantee.

## 9. Executable checks and domain outcomes

A check has to name what it evaluates, how it runs, and how its result is interpreted. Required checks must not be skipped because a software-oriented review router considers the code diff low risk.

The initial design compiles required analytics checks into explicit ordinary DAG tasks. This reuses the current executor, logs, timeouts, and dependencies while keeping analytical outcome separate from process status. Named reviewers can serve code or methodological review; their existing redo semantics must not silently change the analysis.

Contracts use one versioned, portable schema, validated at admission and output ingestion by the TypeScript controller. Python helpers may use a matching typed model, but there must be one normative schema and shared conformance fixtures. A worker request binds run/task/attempt identity, plan revision, exact inputs, validator or program version, command arguments, allowed output paths, declared access, and timeout. It declares the output schema before execution. The compiler passes arguments through the executor safely rather than interpolating question text, source metadata, or untrusted paths into shell code.

An illustrative execution declaration (the script is a proposed fixture, not an existing repository command):

~~~json
{
  "schemaVersion": 1,
  "kind": "check",
  "id": "check-split-1",
  "planRef": "plan-churn@1",
  "validatorRef": "entity-overlap@1",
  "inputRefs": ["artifact-split-1"],
  "argv": ["python", "checks/entity_overlap.py", "--request", "request.json"],
  "output": {"path": "check-result.json", "schema": "check-result@1"},
  "timeoutSeconds": 60,
  "automaticRetries": 0
}
~~~

Admission resolves the symbolic references to exact versions/digests, creates the execution-specific request file, and records the resolved request. The path alone never identifies which result is authoritative.

Proposed check-result envelope, with illustrative identifiers:

~~~json
{
  "schemaVersion": 1,
  "checkId": "check-split-1",
  "executionId": "execution-check-1",
  "planRef": "plan-churn@1",
  "policyRef": "predictive-local@1",
  "validator": {
    "id": "entity-overlap",
    "version": "1",
    "codeRef": "artifact-validator-1"
  },
  "subjects": [
    {"artifactRef": "artifact-split-1", "digestRef": "digest-split-1"}
  ],
  "outcome": "fail",
  "observed": {"overlappingEntities": 12},
  "expected": {"overlappingEntities": 0},
  "evidenceRefs": ["artifact-overlap-summary-1"],
  "reason": "Entity overlap violates this plan's split contract."
}
~~~

A command that successfully computes and writes this envelope can exit zero even though the check outcome is fail. That means “the check ran”; the analytics controller blocks the dependent claim's promotion. A malformed or missing envelope, checker crash, subject mismatch, or unexpected exit is an execution/evidence error. The controller must run final reporting even if an analysis branch cannot proceed, so failures remain explainable.

Check outcomes are `pass`, `fail`, `inconclusive`, `not_applicable`, or `error` as defined in the [canonical state vocabulary](state-vocabularies.md). A `not_applicable` outcome requires a policy-supported reason. Missing output is not `not_applicable`. The controller verifies required-check coverage against the frozen policy; the worker cannot drop a failed check from its submitted list. Feature 010 owns the dependent Claim's supported-presentation eligibility rule.

Required policy comes from a versioned profile approved by the project owner, with tested baseline validators. The worker supplies task-specific parameters and can propose additional checks; it cannot replace a required validator with a permissive generated script. A genuinely new method may require a new profile before its claims qualify for the intended use. Policy changes are a distinct authorized operation, never an automatic response to a rejected result. Under the local trust model this is controller-enforced workflow authority, not protection against a process that can rewrite the controller itself.

Separate three concerns:

1. **Integrity checks:** did the specified measurement and computation happen on the correct data?
2. **Analytical observations:** what did the comparison show, including null or negative results?
3. **Use criteria:** does the result satisfy the threshold needed for a proposed decision or deployment?

“Candidate does not beat baseline” can be a valid analytical observation and a failed deployment criterion while the task itself is successful. It must not cause repeated training until it wins.

Validators should be independently meaningful: recompute a critical quantity where feasible, use trusted method implementations, or test a known corruption. A generated test that merely repeats the worker's own assumption is weak evidence. Binding a check to a file hash establishes which bytes it checked, not that its methodology is correct.

Each validator declares the failure classes it detects and its limits. An entity-overlap check establishes the tested partition property; it does not certify the absence of temporal, semantic, or preprocessing leakage. The viewer must preserve those narrower meanings when summarizing passed checks.

## 10. Recovery, parallel work, budgets, and stopping

| Event | Required handling |
| --- | --- |
| Transient infrastructure failure | Retry only a classified, idempotent operation within the same frozen inputs and budget; preserve the failed attempt. |
| Worker or code failure | Diagnose, then admit an explicit repair execution. A changed program gets a new code digest; no opaque method mutation inside a retry. |
| Failed data or integrity check | Retain evidence and block affected claims; correct the cause through a recorded repair or return the limitation. |
| Inconclusive/negative/conflicting finding | Treat as an analytical outcome, not a process failure. |
| New method, source window, or outcome-informed choice | Admit a new plan revision or planned recurrence, with prior exposure retained. |
| Cancellation or budget exhaustion | Stop admitting work, cancel active work according to the declared termination policy, preserve partial artifacts, report remaining uncertainty. |
| Crash during output publication | Recover the last committed manifest; ignore or reconcile uncommitted outputs, never present them as validated. |

Automatic retries default off for agent analysis tasks. Initially use maxAttempts 1, reviewRounds 0, repairRounds 0, and no automatic harness fallback for the analytical task. The controller can schedule a classified infrastructure retry or an explicit repair. Generic runner retries and fallbacks are not evidence-aware.

Each parallel task writes to an execution-specific output directory. Workers do not share writable notebooks, result paths, or mutable analysis datasets. Code worktrees are optional for code authoring; they are not a data sandbox. CPU-bound libraries must not multiply the runner's concurrency into uncontrolled nested parallelism.

Computation requests declare relevant CPU/GPU, memory, temporary disk, output-size, and source-query allowances. Admission reserves scarce capacity across concurrent tasks. Each adapter distinguishes a measured or enforceable limit from an estimate: a local scheduler reservation is not an OS memory limit, and a SQL timeout is not necessarily a bytes-scanned cap. Reject work that requires an unavailable enforcement guarantee; otherwise disclose the weaker profile. Large source reads and outputs require bounded extraction or an explicit budget revision, not an agent's unrestricted discovery query.

Every inquiry declares finite limits on elapsed time, worker invocations, admitted branches, plan revisions, and command timeouts. A fixture/demo ships with a tested default budget; production values are user-configurable. A dry estimate is informative, not a promise of exact cost.

The first local fixture profile admits 15 minutes of work, 12 worker
invocations, four optional branches, three Plan revisions, and five minutes
per command. The controller enforces count and command-timeout limits. It
advertises hard active-process cancellation only after the host adapter
passes its cancellation test; otherwise 15 minutes is a scheduling cutoff.

The controller reserves capacity before parallel launch and reconciles actual usage afterward. It reserves time/work for mandatory validation and final reporting before admitting optional exploration. If full reporting cannot run, a deterministic partial-status renderer still exposes the committed evidence and stop reason without requiring another model call. Unknown token/cost usage is marked unavailable; fall back to enforceable invocation and time limits. A local process wrapper cannot honestly advertise an exact token or monetary cap if the harness provides no enforcement or telemetry.

Distinguish a scheduling deadline, after which no tasks launch, from a hard cancellation deadline for active tasks. The existing runner's wall-clock scheduling limit must not be advertised as the latter without the needed termination behavior.

Stop on repeated no-progress proposals or inability to resolve required evidence within budget. Partial answers identify completed checks, absent evidence, consumed allowances, and the highest-value next action. The worker cannot silently raise its budget.

## 11. Storage, artifact publication, and recovery

Use one shared scheduler and a versioned analytics sidecar. Proposed layout:

~~~text
<project>/
  dag.run.json
  dag.run.d/
    analytics/
      manifest.json
      records/
      executions/<execution-id>/
      artifacts/
      reports/
  .dag-analytics/
    sources/<source-id>/
      manifest.json
      skills/<version>/
    memory/
      manifest.json
      records/
      catalog.sqlite
~~~

Run-scoped records belong beside the run so archival preserves them. The project source registry exists separately so later runs can reuse skills. Runs pin exact source-skill versions; archiving or removing a run must not destroy a version another run references.

The project memory area holds versioned curation records and a rebuildable catalog index. Run manifests remain authoritative for execution evidence; project memory records are authoritative for pins, story grouping, and skill-candidate decisions. The catalog indexes only committed records and is reconciled after crashes. A failed indexing pass cannot turn a partial run artifact into a published result. See §21 for the retrieval and skill-capture contract.

The controller is the sole logical writer of committed domain records. Workers write candidate outputs to execution staging directories. The controller validates identity, schema, artifact existence, digest, and required checks; moves or registers complete artifacts; then atomically publishes the new manifest revision. Commit ID and expected manifest revision make retries idempotent and detect conflicting writes.

The output declaration also specifies artifact role, count and byte limits, and retention class. Workers receive managed scratch paths, not the project root as an intended output location. The default deliverable profile permits no CSV or workbook exports; those are generated only for a user request or a configured deliverable. The controller rejects undeclared exports or a count/size overrun until a scoped budget revision is admitted. A result can be stored as a structured record and reproducible computation without a separate spreadsheet for every intermediate table. An unrestricted shell can still write outside declared paths: only an enforced write boundary justifies a no-sprawl guarantee. See §21.5.

Recording evidence and qualifying a claim are separate operations. Well-formed failed checks, negative results, and execution errors are committed with their actual states. Passing all checks is never a prerequisite for retaining the record; the required evidence determines subsequent claim qualification and use. Malformed submissions remain available as diagnostic artifacts without masquerading as valid domain records.

Use a single-writer transaction protocol over existing atomic-write primitives; do not assume several atomic JSON writes are one transaction. Recovery reconciles the manifest's committed references before scheduling new work. A crash after computation but before publication must not duplicate a logical result or make partial data visible.

Cache immutable computations only when their effective inputs, code, environment, seed, and execution contract match. Policy changes can require new checks without necessarily recomputing the measurement. A cached result does not imply cached authorization for a new use. Keep caching disabled in the first slice.

Reproduction must declare its comparison rule: exact bytes, canonical table content, or stated numerical tolerances. Timestamps or nondeterministic serialization can change a file hash without changing an analytical result. Preserve both byte integrity and the declared semantic comparison.

Append-only history is an application contract under the local trust model, not tamper-proof storage. A privileged process can rewrite files and hashes. Stronger audit guarantees require independently protected storage or logging. Retention and required deletion may remove data; retain permissible metadata/tombstones and mark the evidence unavailable instead of promising permanent reproducibility.

The execution profile declares permitted sources, write roots, network destinations, and how those restrictions are enforced. Use source schemas and bounded aggregates as agent context when raw records are unnecessary; processing code may require row access under the declared profile. Credentials must not appear in task specs, reports, skills, or command logs. Local unrestricted execution does not qualify as isolation merely because the request lists restrictions. A source-specific read-only database account can enforce database permissions without claiming the entire worker is sandboxed.

## 12. Claims, review, and intended use

Store execution, validation, Claim evidence state, reproducibility, review, and authorization separately using the [canonical state vocabulary](state-vocabularies.md). Claim evidence state is `supported`, `unsupported`, `inconclusive`, or `conflicting`; reproduction is `not_attempted`, `reproduced` under a stated rule, `mismatched`, or `unavailable`. Review records preserve actor type and unresolved issues. Authorization always states the requested use.

All dimensions are not expected to “pass” in every analysis. A reproduction may not yet be attempted, a causal assumption may be untestable, and conflicting results may be the correct answer. The applicable policy determines what is required for this question and use.

Default behavior:

| Situation | Worker/controller response |
| --- | --- |
| Routine internal answer with adequate evidence | Return it with recorded worker review and explicit limitations. |
| Inconclusive or conflicting internal evidence | Return that conclusion; additional research is optional and budgeted. |
| Required integrity evidence is missing or fails | Block supported presentation of affected claims; report the failure and preserve partial work. |
| Post-observation exploration | Permit it with permanent origin/exposure labels. No human click can clear those labels. |
| Consequential decision, external publication, or unresolved material judgment | Evaluate current evidence and intended use; request focused review or authorization as configured. |
| Human accepts uncertainty for a decision | Record an exception or risk acceptance attached to that use. Keep the evidence states unchanged. |

Mode determines methodological requirements; a simple descriptive number can have high stakes. Causal interpretation has stringent evidence requirements, but a mode label alone is not a universal human-gate switch.

Record intended use at intake. Re-evaluate when a Claim is linked to a Decision, when a requested publication is prepared, or when its scope/use changes. Saving an internal report file is not automatically external publication. Off-platform copying cannot be detected.

Authorization binds to exact claim, result, check, and policy revisions. New material evidence or a changed artifact makes prior authorization stale for that use. Historic decisions remain auditable; the system flags changed evidence rather than rewriting the decision.

A review gate must identify the actual issue, present the relevant trace, and require a rationale. Recording that someone opened a trace is not proof of comprehension. Worker review, separate-agent review, and human review are labeled distinctly; multiple agents using the same inputs do not constitute independent empirical evidence.

Later, sample routine reviews for audit using a recorded sampling frame, policy version, risk strata, and selection rule. Record disagreement severity, sample size, adjudication, and uncertainty. Do not equate disagreement with ground truth or automatically change policy thresholds from tiny samples. Audit-driven policy changes create new versions; they are not silent self-modification.

## 13. Reusable source skills

Source skills are a core product capability: learn a database or recurring stream once, preserve useful operational knowledge, then reuse and refresh it.

A skill is scoped by source identity, environment, tenant/access role, and relevant schema/definition versions. Matching a table name or schema fingerprint alone is insufficient. Two sources can share structure but differ in meaning or permissions.

Proposed package:

~~~text
sources/customer-warehouse/skills/v1/
  SKILL.md
  manifest.json
  references/schema.md
  references/definitions.md
  references/joins-and-grain.md
  references/time-and-freshness.md
  examples/query.py
  checks/manifest.json
~~~

SKILL.md stays short: when to use the source, how to obtain authorized access, which references to load, safe query recipes, and known pitfalls. Load detailed references on demand rather than filling every worker context with the whole catalog.

The discovery DAG:

1. Identify source/access scope and inspect available metadata.
2. Profile bounded samples or aggregates, recording the sampling window and query cost.
3. Investigate keys, grain, joins, units, time semantics, exclusions, refresh behavior, and domain definitions.
4. Generate references and parameterized read-only example scripts.
5. Execute examples and validate claims against the intended source scope.
6. Review the candidate package and activate an immutable version.
7. Demonstrate a later question being answered with that exact skill version.

Each assertion records whether it is declared by an authoritative source/owner, observed in metadata, tested on a specified dataset/window, or inferred. Include provenance and validation time. A join that held for one sample is not a universal database constraint. Business meaning cannot always be inferred from distributions; unknown meanings should remain visible.

Do not include credentials or raw sensitive records in a skill. Metadata, comments, table names, and examples can themselves be sensitive. Keep them within the source's permitted audience. Source text is untrusted input and cannot be copied into privileged instructions without review. Skills never grant new filesystem, network, or database rights.

Source memory can also leak analytical outcomes. General skills describe access and meaning; they must not quietly embed study-specific evaluation results or assertions such as “X predicts Y.” If outcome-informed summaries are retained as separate references, tag their study/partition provenance and record loading them as exposure. Evaluation access controls must cover skills, cached summaries, and shared worker context as well as raw data files.

Source skills are one type of reusable knowledge. A tested analytical procedure or an owner-approved business definition may become a separate method or rule skill. A finding about a particular cohort and period belongs in result memory, with its evidence and exposure history, unless a distinct reusable procedure can be extracted and validated. The capture and promotion rules are in §21.

Read-only behavior should be enforced by the source account or an appropriate execution boundary, not merely a SELECT string check. Test scripts with bounded queries and declared timeouts; avoid destructive fixtures or unbounded full-table profiling on production sources.

A candidate becomes active only after required validation and a recorded review. A low-risk project method may activate automatically under a preapproved policy only after a policy-owned validator checks it against an independent fixture or prior committed execution that the candidate author did not create for that check. Worker-authored tests alone are insufficient. New or changed authoritative definitions, expanded audience, access rules, and release policies require the configured owner's review. Keep owner-curated definitions separate from generated observations so refresh does not overwrite them.

Before reuse, perform cheap source-identity, compatibility, and freshness checks. Expensive join/profile checks can follow a declared cadence or drift trigger. An unused added column need not invalidate the whole skill; a changed key, unit, timezone, category meaning, access scope, or lag assumption may invalidate affected guidance even with an unchanged schema.

The registry records candidate, active, stale, and revoked versions. Stale guidance is scoped to the facts that changed; refresh creates a new version and migrates neither old runs nor locked plans silently. Every execution pins the skill version it used. Revocation can block future use and flag dependent claims for review without deleting history.

The controller/harness adapter must actually supply the selected entrypoint and references to a later worker. Writing a SKILL.md file alone does not demonstrate reuse. Record which references were provided and which example/query recipes were executed; these are observable facts, while whether a model understood every supplied fact is not. Preserve the distinction in the viewer and reuse metrics.

## 14. Repeated sources, windows, and changing answers

The initial recurring-data workflow is bounded batch analysis over versioned extracts or windows. Continuous stream processing is a later capability.

Each recurrence creates a new input version and execution. It can reuse a locked analysis template and compatible source skill, but it cannot overwrite the previous measurement. Record window identity, completion/watermark status, source positions, label maturity, and late-data corrections.

Before comparing periods, check that metric definitions, population coverage, weighting, observation windows, and source freshness are comparable. Distinguish data drift, definition drift, model-performance drift, and skill staleness; they imply different corrective actions.

Do not treat repeated monitoring as unlimited fresh hypothesis tests. The monitoring plan needs an alerting/inspection policy, and any inferential claims must use a method appropriate to repeated looks. Automatic alerts and external notifications require the user's configured authorization.

An upstream correction traverses recorded dependencies: identify affected results, claims, reports, skills, and use assessments; mark their current validity explicitly; then schedule bounded re-evaluation. Historical versions remain available with the correction relationship.

## 15. The user experience and report contract

The default screen should answer the user's question. Show the answer, key chart/table, population/period, limitations, and whether further work is needed. Keep detailed runtime fields behind an evidence inspector.

A reported number or claim opens a trace:

**Report assertion → Claim → Result → Checks/Review → Execution → Transformations → Dataset version**

The inspector shows the plan and observation history, denominator reconciliation, code/query, environment, skill version, and exact policy applied. Broken or stale evidence links are visible. Do not render an unqualified “verified” badge.

Reports bind numerical values and critical factual statements to result selectors, such as a metric key or a table cell. Formatting records units, rounding, and denominators. Narrative review checks that wording preserves scope and distinguishes prediction, association, and causation. Not every connective sentence needs an evidence object.

Charts retain their input table, plotting code, labels, aggregation, and uncertainty definition. A visually persuasive chart with an unexplained denominator fails the report contract.

Generated HTML, source comments, and evidence text must be escaped or sanitized before display. Serve only declared artifacts inside approved roots; do not turn arbitrary worker-supplied paths into file-serving permissions.

Exports include the evidence manifest and version references where allowed. Readers without data access see metadata and limitations rather than a misleading promise that they can reproduce the analysis.

A project library complements the report view: search by question, source, metric, period, claim, status, and user pin; open a concise answer card and then its evidence trace. Project home shows recent, pinned, and unresolved answers. A later related question searches this library during orientation and surfaces compatible prior findings with their current limits, without requiring the user to remember that the analysis happened. Unpromoted outputs remain findable in an artifact inventory without crowding the answer library. A recovered legacy spreadsheet can be indexed with unknown provenance rather than shown as a validated result.

## 16. Concrete integration with the current repository

The existing implementation was inspected for this revision. These are extension points, not claims that analytics behavior already exists.

| Existing component | Reuse | Required analytics work |
| --- | --- | --- |
| [DagRunner](../../src/runner.ts) | Command execution, dependency scheduling, timeouts, logs, recovery. | Controller admissions, classified retry behavior, budget cancellation semantics, exact execution bindings. |
| [Reviewer policy](../../src/review-policy.ts) and [types](../../src/types.ts) | Named review commands, verdict extraction, task/gate records. | Explicit mandatory check tasks and structured domain results. Current reviewer rejection can redo work; software routing must not omit analytics checks. |
| [Store](../../src/store.ts) | Run paths, serialization, atomic write and lock primitives. | Versioned analytics sidecar, committed manifests, recovery reconciliation, project source registry. |
| [Factory controller](../../src/factory/controller.ts) | An example of staged domain work driven through DagRunner. | A separate analytics controller with its own semantics and serialized between-stage admission. |
| [Server](../../src/server.ts) and [viewer](../../viewer/run.html) | Run UI, APIs, existing factory summary. | Question/answer view, exact evidence traces, scoped policy decisions, source-skill versions. |
| [Harness presets](../../src/harnesses.ts) | Non-interactive agent CLI invocation. | Capability discovery, usage availability, prompt/context manifest, output validation, and explicit trust profile. |

This repository owns the analytics product. Keep one execution scheduler within it. The analytics controller decides what evidence work is needed; the neutral runner decides which admitted tasks can execute. Do not create a second worker pool.

During extraction, preserve the current repository history but remove software-factory code, prompts, examples, and default viewer language from the analytics worker's checkout and context. Move genuinely shared scheduling, process, logging, locking, artifact, and recovery behavior behind a neutral package boundary. The software product and analytics product may version and release independently against that boundary. Do not maintain two edited copies of the runner indefinitely.

Workers submit graph-change proposals as artifacts. In the initial design, the controller processes them between settled execution stages under serialized ownership: validate references and acyclicity, record the expected graph revision, persist the next admitted stage, and then resume the existing runner. Workers do not edit a locked run file. Completed executions keep their original bindings.

Two controllers must not advance the same run concurrently. The controller ownership protocol must cover planning/persistence intervals between runner invocations, not only periods when child processes execute. Source-registry updates need their own compare-and-swap or lock across different runs.

The current runner broadly retries failures when attempts remain, and handleRejection can redo a task or repair its ancestors. Disabling those paths for analytics is a release requirement, not a prompt convention. Its wall-clock budget stops new scheduling; a hard deadline additionally needs active process termination.

Start with analytics metadata in its own sidecar so Run.storageVersion need not change just for domain records. If a generic runner contract must change, update serialization, compatibility, and tests deliberately. Keeping the version number unchanged is not more important than correct compatibility.

Factory utilities are reuse candidates, not a preapproved analytics kernel. Budget/evidence storage primitives may fit. Repository-scoped negative-defect evidence is not the same as a statistical null result; reviewer calibration is not the probability an analytical hypothesis is true. Extract shared code only after both consumers demonstrate matching semantics.

Optional integrations such as MLflow, DVC, or a validation library can supply existing capabilities through adapters. No three-tool stack is required. The first local worker must remain runnable without a paid service.

## 17. Worked first workflow

**User question:** “Does a decline in recent usage help predict churn beyond what we already know from tenure and subscription plan?”

Use a synthetic relational fixture with known generation rules, immutable tables, a stable event timeline, a deliberately broken variant, and a reference evaluator that is not supplied to the worker.

The worker:

1. Resolves churn horizon, observation grain, cohort, and prediction cutoff; records any assumption it had to make.
2. Acquires a fixed snapshot and reconciles source rows to eligible customer observations.
3. Explores development data and records that this question or refinement was informed by exploration.
4. Locks a bounded comparison between a baseline and a comparable model with usage features, including search space, temporal/group split, metric, uncertainty method, and stopping rule.
5. Uses features available at each prediction time and outcomes with enough follow-up to be labeled. Fits preprocessing within training folds.
6. Runs the comparison and its mandatory checks. A lack of improvement is retained as a valid answer.
7. Evaluates the chosen procedure on the workflow-withheld partition with the access limitation shown.
8. Produces a scoped claim and a report whose numbers are bound to results.
9. Exposes the evidence trace in the viewer and reproduces the computation under the declared comparison rule.

A metric value is not hard-coded into the proposal or acceptance criterion. The prototype succeeds by correctly answering this fixture, including its null variant, and catching the seeded invalid analyses.

A second run introduces a changed source window or definition. It must reuse the source skill where valid, detect affected guidance, explain comparability, and preserve the old answer.

For example, the visible history can be **Q1 → exploratory R1 → locked P1 → E1/checks → R2 → C1**. An outcome-informed method change creates **P2**, linked to P1 with its reason and exposure, and produces a new claim C2. C2 may challenge C1 only after comparing their scopes; a different population may instead support a separate scoped claim. A review records that distinction. A refined Q2 links to Q1, while Q1, both plans, and both claims remain inspectable. A later decision references the exact claim revision used, even if subsequent evidence changes the current answer.

## 18. Delivery sequence with real consumers

Each slice ends with a working user path. Parallel module implementation is allowed only when integration and acceptance are included in the same slice.

| Slice | User-visible capability | Exit condition |
| --- | --- | --- |
| A — executable evidence | Run one fixed analysis plus checks and open its evidence. | Structured pass/fail/inconclusive outcomes bind to exact artifacts; a negative result survives without retry; crash recovery does not publish partial evidence. |
| B — delegated inquiry | Supply the question and fixture; the worker authors the plan/code/checks and answers. | Denominator drift and post-observation revision are detected, the answer uses the records, the viewer trace works, and the worker stops within budget. |
| B2 — result library and work ledger | Register generated outputs and import a crowded folder; preserve each attempt and inquiry answer, then recall them when a related question arrives. | Every admitted analysis has an evidence-linked ledger entry and each inquiry has an answer card, even if the worker omits its summary; a later related inquiry surfaces compatible prior work without a user hint; a 150-card ranking fixture meets the declared Hit@3 target; at least 99 mixed CSV/workbook files can be indexed and triaged without inventing provenance or treating every file as a finding. |
| C — source memory | Connect one read-only SQL source or bounded stream extract, generate a skill, and answer a second question using it. | Demonstrated reuse, scoped semantic checks, drift/revocation behavior, and no credentials or raw sensitive rows in the package. |
| C2 — proactive skill capture | Observe a valued or repeated analytical procedure or business rule and produce a scoped candidate automatically. | The candidate has a trigger reason, evidence, tests, authority, and activation decision; a later worker uses the right version without receiving unrelated outcome findings. |
| D — recurrence and review | Re-run on a new window and inspect changed claims or requested use. | Comparability checks, dependency invalidation, use-policy re-evaluation, and a complete audit trail. |
| E — broader analytics | Add one inferential or causal profile for a concrete user task. | Method-specific reference fixtures, failure cases, UI interpretation, and explicit limits pass the same end-to-end bar. |

Question, plan, execution, result, claim, and supporting manifests grow through these slices. No independent “build all objects first” phase.

A slice requires a production caller, deterministic acceptance checks, a live worker demonstration, a visible output, and tested failure behavior. Unit tests alone do not complete it. Once a slice works, measure its value before adding another service or abstraction.

Distributed execution, a notebook editor, broad source connectors, automatic causal discovery, a marketplace of skills, a new model runtime, and full stream processing are deferred. A truly isolated evaluator is a separate capability milestone with access-denial tests.

## 19. Adversarial acceptance matrix

These tests are requirements for the owning slice. Fixture commands and IDs must be implemented with that slice; this document does not invent commands that already exist.

| ID | Injected case | Required observable behavior | Slice |
| --- | --- | --- | --- |
| T01 | Task exits zero without its declared result | Evidence error; no supported report value. | A |
| T02 | A check result refers to an older artifact | Subject mismatch; old pass is not reused. | A |
| T03 | Check executes successfully and finds entity overlap | Check task completes, validation fails, supported presentation of the dependent Claim is blocked, and evidence remains inspectable. | A |
| T04 | Candidate fails to improve over baseline | Valid negative answer; no retry toward a favorable metric. | A/B |
| T05 | Duplicate join or unexplained population filter | Denominator/grain discrepancy appears in the trace and blocks supported presentation until a validated transformation resolves it. | B |
| T06 | Outcome-informed method revision | New plan/execution; prior exposure remains; approval cannot restore pre-specification. | B |
| T07 | Planned resample or declared search candidate | Recorded planned execution, not falsely labeled post-hoc merely because an earlier planned result exists. | B |
| T08 | Future feature, immature label, or preprocessing on all rows | Specific temporal/leakage check fails; supported presentation of the affected Claim is blocked. | B |
| T09 | Exploration inspects evaluation metrics early | Known exposure changes the lineage; unknown out-of-tool access is not declared impossible. | B |
| T10 | Required check is omitted, skipped, or renamed by the worker | Frozen-policy coverage fails closed; no supported promotion. | A/B |
| T11 | Report swaps denominator, unit, or metric value | Result-binding validation blocks the incorrect assertion. | B |
| T12 | Worker loops or exceeds admission/time allowance | No new work; bounded termination behavior and partial answer with remaining gaps. | B |
| T13 | Crash between artifact write and manifest commit | Resume produces no duplicate logical result and exposes no partial success. | A |
| T14 | Two tasks or controllers race on one output/state revision | Artifacts remain separate; exactly one expected-revision commit succeeds and the other receives a revision-conflict result without overwrite. | A/B |
| T15 | Reproduction changes byte serialization but not values | Declared semantic comparison distinguishes it from a real numerical mismatch. | A/B |
| T16 | Claim requested for a new consequential use | Exact current evidence is reassessed; required authorization precedes the use. | D |
| T17 | Schema comment or source text contains instructions | Remains untrusted data; cannot alter access, policies, or activated skill instructions. | C |
| T18 | Same schema, changed unit/key/role or stale watermark | Affected skill guidance becomes `stale` and cannot guide current work. A validated replacement is used when available; otherwise the answer states the unresolved assumption. | C |
| T19 | Second inquiry reuses a skill | Exact version and examples consumed are recorded; redundant discovery is reduced without dropping required checks. | C |
| T20 | Late event corrects a previously analyzed window | New version; downstream claims flagged and comparability assessed; history retained. | D |
| T21 | Uncertain result is returned internally | Honest completion is allowed; no mandatory human interruption solely for uncertainty. | B |
| T22 | Evidence disappears or is redacted | Availability changes visibly; historic existence does not imply current reproducibility. | A/D |
| T23 | Agent changes code or policy after validation | Digest/revision binding invalidates the old check or authorization. | A/B |
| T24 | Local agent can read the withheld file | UI still states local access is not isolated; no false independence badge. | B |
| T25 | Source skill or shared summary contains evaluation findings | Outcome provenance is retained; activation/loading cannot silently restore a pre-observation claim or bypass an enforced evaluation boundary. | C |
| T26 | Optional exploration consumes the remaining allowance | Required finalization was reserved, or a deterministic partial report appears without an extra model invocation. | B |
| T27 | Harness does not expose token/cost usage | Usage remains unavailable; enforced invocation/time limits still apply and no exact spend cap is advertised. | B |
| T28 | A committed output is absent from the catalog after a crash | Reconciliation adds one correctly linked inventory entry; no partial output is promoted. | B2 |
| T29 | A folder contains 99 mixed CSV and workbook files | All readable files are inventoried and grouped for triage; none receives fabricated execution provenance or a story solely from its filename. | B2 |
| T30 | Two legacy files have identical bytes but distinct paths | Duplicate content is indicated while both original file identities remain findable. | B2 |
| T31 | A negative or inconclusive answer is later searched by question | Its answer card, scope, uncertainty, and evidence trace are retrieved without being mislabeled as a failed run. | B2 |
| T32 | The user says they are happy with a completed analytical procedure | A method-skill candidate is created with trigger, scope, evidence, and tests without requiring the phrase “make a skill.” | C2 |
| T33 | A striking result came from an unvalidated method | Statistical significance or model enthusiasm alone cannot activate a reusable skill. | C2 |
| T34 | A proposed business rule conflicts with an authoritative definition | The conflict and source authority are shown; the new rule is not silently activated or applied. | C2 |
| T35 | Prior evaluation findings appear in retrieved memory | Loading them records exposure and cannot silently restore a pre-observation label. | B2/C2 |
| T36 | A corrected dataset makes a pinned answer stale | Search retains the historical answer with a stale signal and links to the correction; current use is reassessed. | B2/D |
| T37 | The catalog index is lost or corrupt | It is rebuilt from committed evidence and curation records, preserving stable IDs, pins, and link status. | B2 |
| T38 | A tested procedure is repeatedly reconstructed without a direct user request | The capture policy raises one deduplicated skill candidate and records why it was proposed. | C2 |
| T39 | A worker searches memory without access to a restricted workbook or skill | Restricted titles and contents are absent from the search response and model context; the denied lookup is auditable. | B2/C2 |
| T40 | A user says “remember this analysis” after an inquiry | The answer is pinned with its scoped claims and evidence; no reusable procedure is invented unless an independent skill trigger and validation exist. | B2/C2 |
| T41 | A worker finishes or stops without writing an answer summary | The controller creates a minimal card from committed structured state, labels missing interpretation or incomplete work, and does not mark the inquiry fully finalized until the memory record is durable. | B2 |
| T42 | A later worker receives a related question without mentioning the old analysis | Relevant prior answer cards are surfaced with scope and validity; the worker records what it used, rejects a mismatched cohort or stale answer as current evidence, and preserves any outcome exposure. | B2/C2 |
| T43 | Exploration creates 1,000 temporary tables and requests 1,000 workbook exports | Scratch work stays in managed space; default export policy publishes no workbooks and rejects the undeclared or over-budget request without losing committed results. | B/B2 |
| T44 | An inquiry completes with unused intermediate files | The controller retains evidence and selected deliverables, then removes only eligible managed scratch files under the retention policy; the answer and result trace remain intact. | B2 |
| T45 | The optional notebook-delivery profile is enabled and a user requests an editable notebook | The notebook is executed from a fresh state against pinned inputs, and displayed claims bind to committed results; a dirty or non-replaying notebook is labeled exploratory and cannot be authoritative evidence. | Optional notebook profile |
| T46 | A user requests a workbook after an answer is recorded | One declared export is generated from the committed result version, registered with provenance, and linked to the answer; the original result remains independent of the workbook. | B2 |
| T47 | An unrestricted local worker writes a workbook outside the managed output root | The controller flags the unmanaged file and does not present the output policy as enforced; routine cleanup never deletes the user-owned location. | B2 |
| T48 | A worker runs a method and later omits the narrative or crashes | The ledger still states the question, method, exact data version, execution status, committed results or absence, and what interpretation is missing. | B2 |
| T49 | A later worker proposes the same question, data version, population, method, and parameters | The prior attempt is surfaced before execution with an exact-match reason; the worker reuses valid evidence or records a deliberate rerun reason. | B2 |
| T50 | The prior analysis found no clear effect or ended with a failed check | It remains searchable as work done, with its null/inconclusive/failed state; no worker silently repeats it as though nothing was tried. | B2 |
| T51 | A similar question uses a newer window or different cohort | Prior work is shown as related, not identical; the new execution proceeds with a comparison link and a reason for the changed scope. | B2/D |
| T52 | The controller commits evidence, then crashes before acknowledging the worker | Recovery finds the committed revision; replay with the same commit ID yields one logical result, with no partial or duplicate publication. | A |
| T53 | Default analytics worker searches its checkout and context for factory prompts and tasks | Factory instructions and fixtures are absent from its normal discovery path; neutral execution still runs a fixed analytics task. | Foundation/001 |
| T54 | Planned two-group renewal-rate difference on a valid fixed fixture | The profile reports the predeclared estimate, interval method and level, observation unit, denominator, assumptions, and scoped interpretation against a reference evaluator. | E |
| T55 | Repeated units, missing group membership, and post-result subgroup selection are seeded | Invalid independence and denominator assumptions block supported presentation; post-result selection remains exploratory with exposure visible. | E |
| T56 | Twenty annotated questions search a 150-card library with keyword, cohort, and period distractors | At least 18 primary relevant cards appear in the top three; the ranking report records Hit@3, match reasons, and zero restricted results. | B2 |
| T57 | Empty corpus and five no-match questions | No direct answer is fabricated; related cards, if any, are labeled with mismatch reasons. | B2 |
| T58 | An inferential comparison is inspected repeatedly and stopped when favorable | An undeclared look is recorded as exposure and cannot retain the original fixed-look inferential claim; declared sequential inspection follows its locked rule. | E/D |
| T59 | A configured owner explicitly approves a scoped business rule | The same concrete approval satisfies authority once; the version activates only after remaining tests and conflict checks pass, with no redundant prompt. | C2 |
| T60 | A report artifact contains active markup or an out-of-root path | Markup is inert in the viewer and the path grants no file access; the attempted unsafe artifact is visible as a failed rendering case. | A/B |
| T61 | A replacement worker starts after earlier evaluation exposure | Its context includes that exposure and the plan retains its post-observation label; no restart resets the evidence boundary. | B |

Invariant coverage: I01 → T01/T02/T23; I02 → T11/T22/T54; I03 → T02/T23; I04 → T03/T04/T55; I05 → T06/T07/T09/T61; I06 → T10/T23; I07 → T05/T08/T20; I08 → T09/T22/T27; I09 → T16/T23; I10 → T12/T26/T27; I11 → T17/T18/T19/T25/T59; I12 → T13/T14/T52; I13 → T24/T25/T53; I14 → slice exit conditions plus T11/T19/T31/T53; I15 → T28/T29/T30/T39; I16 → T32/T33/T34/T38/T59; I17 → T31/T35/T36/T40/T42/T56/T57; I18 → T28/T37/T41; I19 → T43/T44/T45/T46/T47; I20 → T48/T49/T50/T51.

Mechanical contract tests should be deterministic. End-to-end worker evaluations should run across multiple recorded seeds/sessions, with fixed inputs, harness versions, budgets, and evaluator rules. Keep benchmark answer keys outside the worker's allowed context.

Passing this matrix establishes bounded properties of the tested system. It does not prove universal statistical correctness or immunity to a malicious local administrator.

## 20. Evaluation, success criteria, and remaining decisions

Measure the product on complete user questions, not numbers of tasks, agents, artifacts, or passing reviews.

Track:

- Correct answers and correctly qualified uncertainty on reference questions.
- Rate of unsupported assertions, caught injected faults, and false blocking.
- Evidence-trace completeness and time needed for a reviewer to verify a reported value.
- Completion rate within declared budgets, elapsed time, invocations, and cost where observable.
- Warm-source versus cold-source discovery work and quality after skill reuse.
- Fraction of completed inquiries retrievable by question, time to find a prior answer or attempt, relevant prior work surfaced in later inquiries, avoidable duplicate executions, intentional reruns correctly distinguished, ignored or incorrectly reused prior answers, catalog coverage of generated outputs, orphan or falsely curated artifact rates, and retained file count/bytes per inquiry.
- Useful skill-candidate precision, accepted/reused candidate rate, and avoided repeated discovery or method reconstruction.
- Recovery correctness, gate burden, and audited review disagreement with sample sizes.

Compare the same tasks and budgets against an ordinary agent CLI workflow and a simple script/notebook baseline. Use held-out benchmark cases; do not tune the worker on every evaluator failure and then report those same cases as generalization. Set numerical release thresholds before the evaluation batch. No “best in the world” claim is supported until there is comparative evidence.

Feature 018 owns the versioned synthetic fixtures, hidden reference
evaluator, rubric, and release metric registry from the first slice. Its
initial twenty-case minimum is a gate for a named frozen fixture profile,
not statistical evidence of broad reliability. Milestone three-run checks
are smoke/regression tests. A broader reliability claim needs a separately
predeclared target population, sample-size rationale, uncertainty interval,
and held-out evaluation.

Defaults now settled: a dedicated analytics repository using one neutral execution scheduler; worker-authored plans; separate execution/evidence/use states; required checks as explicit tasks; local guarantees labeled honestly; autonomous routine answers; immutable revisions; a project memory catalog; source, method, and rule skills consumed across runs; viewer trace in the first delegated workflow.

Resolve these before the corresponding slice begins:

1. Which real user question and non-sensitive fixture will supplement the synthetic benchmark?
2. Which source and credential boundary will anchor the source-skill slice?
3. Which harness capabilities can actually enforce time, access, and usage limits?
4. What counted use cases need human authorization, and who owns that release policy?
5. What evidence retention and source-access rules apply to the target environment?

The next engineering deliverable is Slice A with a working consumer and failure injection. The proposal is ready to guide implementation when its acceptance cases are owned by that delivery path, not when every future record has a standalone module.

## 21. Project memory, result library, and proactive skill capture

An analytical worker needs two kinds of memory. **Result memory** answers “what did we try and learn about this question, population, and period?” **Skill memory** answers “what reusable rule, source knowledge, or procedure should a later worker follow?” A successful result does not automatically become an instruction. The user should not have to remember the exact filename, prior attempt, or ask the worker to write a skill every time valuable knowledge appears.

### 21.1 Register outputs before deciding what matters

Every committed output receives a stable artifact ID, content digest, media type, creation time, producing execution or import record, declared role, availability, access scope, and links to any Result, Claim, or Report that uses it. Roles include input extract, intermediate table, plot, model, diagnostic, result table, report, and other. The role comes from the execution contract where possible; a model's guess is an attributed suggestion. Staged outputs remain diagnostics until committed.

An exploratory run may create many tables without creating many findings. The artifact inventory keeps them searchable. Every completed or stopped inquiry creates an **answer card** with the question, concise answer or honest non-answer, key claims and results, population, period, uncertainty, source versions, status, and evidence links. The controller makes card persistence part of finalization. If the worker omits its summary, a deterministic fallback card points to committed results and says interpretation is missing; if work stopped early, the card says incomplete. It does not invent a conclusion. Negative and inconclusive answers are cards too. Within a card, the worker proposes which findings belong in the short story using relevance to the question, evidence state, materiality, novelty against prior cards, and stated user interest; it records the reason and links the rest as supporting or exploratory results. This is editorial ranking, not an upgrade to statistical support. A user can pin, title, or annotate a card. Pinning expresses importance to the user; it does not change evidence support. Later corrections mark affected cards stale or superseded while preserving the older version.

This yields three useful views rather than one directory dump:

| View | Contains | Default sorting and use |
| --- | --- | --- |
| Answers | One card per completed or interrupted inquiry, with related claims | Search question, source, metric, period, status, and pins. |
| Work ledger | One compact entry per admitted analysis attempt, including stopped and failed work | See what was tried, method and data version, observed outcome, and reason to continue or rerun. |
| Files | All committed outputs plus explicitly imported legacy files | Inspect role, producer, duplicates, use in claims, and availability. |
| Skills | Candidate, active, stale, and revoked source, method, and rule skills | See trigger, authority, validation, scope, and later use. |

For an existing directory with 99 CSV and workbook files, a bounded importer inventories each file without moving it by default. It records path, digest, size, modified time, format, workbook sheet names or CSV header where access allows, and a limited profile. Duplicate bytes are grouped while each path remains identifiable. It may suggest clusters from names and structure, but it cannot infer that `final_v7.xlsx` is the authoritative answer. If no execution history exists, origin is **unknown**. The user or worker can later attach evidence-backed relationships or create an annotated card without pretending that the old file was validated by this workflow. Changed or missing files receive new availability/version states.

### 21.2 Decide when knowledge deserves a skill

At a bounded post-inquiry checkpoint, the memory controller evaluates recorded capture signals. The worker may propose a candidate; deterministic evidence and project policy decide whether it is eligible. Signals include:

- A user explicitly approves a method, business definition, or answer, including ordinary language such as “this is how we do it” or “I am happy with this.”
- The same validated procedure or source-discovery step is reconstructed in separate inquiries.
- A costly source mapping or domain definition is established with a named authority and expected future use.
- A correction reveals a durable pitfall that can be tested in future work.
- A reusable analytical recipe has defined inputs, outputs, assumptions, and a known failure example.

The system records **why** it proposed the skill, what it would save, which prior material it uses, and whether a similar skill already exists. One enthusiastic sentence, a small p-value, a high model confidence score, or a large number of generated files does not by itself establish a reusable rule. Repeated proposals are deduplicated. Candidate generation has a time and token allowance so memory maintenance cannot consume the analysis budget.

Candidates have three initial types:

| Type | Appropriate content | Required proof before reuse |
| --- | --- | --- |
| Source | Access, tables, grain, joins, freshness, and scoped definitions | Source identity, role, tested recipes, semantic freshness. |
| Method | How to perform a repeatable analysis or transformation | Tested inputs/outputs, assumptions, a successful example, and a failure case. |
| Business rule | An authoritative definition or calculation used by the organization | Named authority, scope, effective period, conflict check, and validating examples. |

Each package has a short worker entrypoint, references, example code or query where useful, tests, provenance, version, audience, and invalidation conditions. A project-scoped method can activate automatically only after its independent policy-owned check and conflict check pass under a preapproved low-risk policy. A new or changed authoritative business definition, expanded audience, access rule, or release policy needs the configured owner's review. If a user explicitly gives a concrete rule with a clear scope and is the configured owner, that input can satisfy the authority requirement; the system should not ask again merely because the rule is important. A generalization from one pleasant result stays a candidate or a result card.

For each new inquiry, orientation searches prior answer cards as well as skills using the question, metric, source, population, and period. It surfaces a small, ranked set with a reason for the match and visible incompatibilities. The next worker receives stable references, current validity, and the minimum context needed to answer the question. It must acknowledge a relevant prior finding or explain why its scope, age, or evidence makes it unusable; a past finding cannot silently answer a new population's question. Loading a prior outcome, especially an evaluation result, creates an exposure record. A stale skill may be shown as background with a warning but cannot silently serve as current instruction. Retrieval records what was supplied and what recipes were executed; it does not assert that the model understood everything supplied.

### 21.3 Local backend and recovery contract

Use a project-local SQLite catalog for lookup and filtering, with full-text indexing for titles, questions, scoped summaries, source names, and tags. SQLite's [FTS5 documentation](https://www.sqlite.org/fts5.html) supports this search design, and its [write-ahead logging documentation](https://www.sqlite.org/wal.html) describes concurrent readers with a writer. This is an implementation choice for the first local product, not a new execution scheduler or a place to copy every large table.

Committed run manifests remain authoritative for execution evidence. Versioned project memory records preserve answer-card curation, pins, ledger rationale and match decisions, skill proposals, and activation decisions. The SQLite catalog is a rebuildable index over those records and committed manifests. The controller updates durable records first, then indexes them idempotently; recovery reconciles any missing index rows. Catalog IDs are stable record IDs, not row numbers. Large CSV, workbook, plot, and model bytes remain in approved artifact locations or their original import location, referenced by digest and access scope. Indexing never promotes an uncommitted output. Sensitive row text is excluded from searchable summaries by default; search results respect the same audience as their source records.

Feature 019 owns artifact identity and the rebuildable index. Feature 023
owns durable analytical attempts and exact-match assessments. Feature 020
owns answer cards, orientation, ranking, and exposure from retrieval. A new
inquiry calls the ledger match once and ranks access-filtered cards from the
index; the index never becomes another authoritative memory store. The
first 150-card lexical retrieval fixture is a measurable target, not a
guarantee that FTS5 will meet it. If the frozen fixture fails, improve the
ranking implementation or report the failed gate; do not soften its labels
after seeing results.

The first search path uses structured filters and full-text matching. Semantic or vector retrieval may be added only if real queries show that lexical search misses important answers and privacy, evaluation, and cost are measured. The first milestone is more basic: someone can ask “what did we learn about churn last quarter?” or “did we already try this model?” and find the answer or attempt, its key result, and the exact evidence behind it, even if the folder contains dozens of discarded intermediates.

### 21.4 Proof of value

The result-library slice uses a fixture with at least 99 mixed CSV/workbook files, duplicates, missing provenance, and one answer linked to several artifacts. Success means all readable files are inventoried, the intended answer is retrievable by question without knowing a filename, a related later inquiry finds it automatically, and no unlabeled file is promoted to an authoritative story. The proactive-skill slice uses two separate inquiries that repeat a procedure, a user approval in natural language, and a conflicting business definition. Success means one deduplicated candidate is proposed at the right scope, safe activation and review decisions are recorded, and a later worker consumes the compatible version. T28–T42 make the failure behavior checkable.

### 21.5 Output discipline and notebook role

The system records analytical results as structured Result objects and evidence-linked answer cards. A spreadsheet is an optional export of a committed result, not the place where the only copy of the result lives. Workers should normally execute versioned Python programs or controlled computation requests and return small structured result payloads, selected plots, and a concise report. Temporary tables can live in the execution workspace; a transformation manifest records how they were produced without publishing one CSV or workbook per step.

Each output is declared as **scratch**, **evidence**, **cache**, or **deliverable**, with count, byte, and retention limits. Scratch is private to an execution and expires under the configured managed-workspace policy after useful diagnostics are captured. Evidence supporting a claim stays available under the project's retention rules; a required deletion changes its availability state and the affected claim's reproducibility status. Replayable cache can be evicted only with its recipe and availability state preserved. Deliverables are explicit user-facing artifacts. The controller never deletes imported files or files outside its managed roots as routine cleanup. A retention sweep has a preview and an auditable record of what it removes.

Default CSV and `.xlsx` export allowance is zero. A user request or configured deliverable profile can authorize a bounded export. An export is rendered from a pinned Result version, registered in the catalog, and linked to its answer card. Its filename is presentation, not identity. Over-budget or undeclared files remain uncommitted diagnostics until the output plan is revised. To guarantee that an inquiry cannot scatter workbook variants into the project root, the worker must run with an enforced writable root. When the chosen local harness lacks that boundary, the controller performs a before/after inventory of project paths, flags unmanaged outputs, and labels the file policy best-effort; it does not silently delete user files.

Jupyter notebooks are **optional work surfaces and deliverables**, not the default system of record. A worker may use a scratch notebook to explore, but an opaque notebook output cannot support a Claim. If the user requests an editable notebook, the controller runs it from a fresh state against pinned inputs before publication, records the environment and execution order, and binds its displayed results to committed Result IDs. A notebook that depends on hidden state or fails a clean rerun remains exploratory with that status visible. This gives people a familiar interactive artifact when useful without making every inquiry produce another large `.ipynb` file.

T43–T47 exercise file explosion, safe cleanup, notebook replay, intentional export, and an unenforced local write boundary.

### 21.6 The analysis ledger prevents accidental repetition

The controller appends or rebuilds one compact ledger entry per admitted analytical attempt from the Question, Plan, Execution, Result, Check Result, and Claim records. An attempt is one scoped analytical comparison or computation, not every shell command or temporary file. Infrastructure retries stay under that attempt with separate execution references; a changed method, data scope, or post-result rerun creates a new linked attempt. A row identifies the question, operational definition, population and period, input dataset version, transformation and method/parameter revisions, execution time and status, observed result or explicit absence, evidence state, limitations, and the worker's next-action rationale. Each summary links to exact records; a worker-written interpretation is marked as such. A failed check, null finding, abandoned branch, or budget stop remains visible as work done. The ledger is a readable index over durable history, not another mutable place where numbers can drift away from their source.

For example, a row might read: “We compared the churn model with and without purchase frequency on the March customer snapshot. The held-out gain was too uncertain to support an incremental-value claim. Do not repeat on this snapshot; revisit when the next window matures.” The row links the plan, model comparison, estimate, check outcomes, and source version. The wording is a summary; the evidence chain remains authoritative.

Before admitting a new analysis, the controller searches the ledger at two levels. An **exact computation match** requires compatible input versions, population, target, transformations, method, code, parameters, and evaluation contract; a **related inquiry** may share the question or metric while differing in one of those dimensions. Exact matches surface the previous outcome before execution. The worker can use still-valid evidence or record a reason to rerun, such as independent replication, changed policy, or suspected defect. A new data window or cohort is related work, not a duplicate, and its comparison link records what changed. Similar wording alone never proves two analyses equivalent. The system does not silently suppress an authorized replication or reuse a stale or failed result as supported evidence.

The viewer exposes a question-level timeline: attempts, findings, non-findings, revisions, and what each attempt caused next. Search and orientation use the same ledger index with access filtering and exposure records. If the worker crashes before writing prose, the controller's fallback row states only what committed structured records establish. Ledger-index loss is repaired from those records and durable rationale events. T48–T51 test recovery, exact repetition, negative work, and intentional new-window analysis.
