# Data Model: Plan revisions and exposure

All persistent records use the frozen `RecordRefV1` identity: stable record ID, positive revision, and content digest. Changes append a new record or event. The 009 manifest defines visibility; temporary/staged bytes and orphan commit files are not history.

## Entities

| Entity | Required data | Owner and relationship |
| --- | --- | --- |
| `QuestionV1` | Original wording; ordered operational interpretations with author/time; intended use; source/question scope; assumptions; unresolved definitions. | 008 registers and retains it; each Plan references the immutable question record. An interpretation edit is a new linked record, not a replacement of user wording. |
| `PlanRegistrationV1` | Frozen discriminated union from interfaces.md: schema version, ID/revision, Question ref, predecessor ref, author, purpose, source bindings, outputs, budget ref; directed fields for the selected profile. | 001 owns schemas; 008 validates, locks, and links revisions. No TypeScript-only copy of a Pydantic model. |
| `RegisteredPlanV1` | `planRef`; exact registration payload; registration time; optional lock time; exposure refs known at lock; canonical `plan_interpretation`. | 008 creates it; the controller alone derives the interpretation. The lock binds the full registration digest. |
| `PlanChangeAssessmentV1` | Prior/new Plan refs; changed locked fields; class (`substantive`, `implementation_only`, `unresolved`); worker reason and author; controller mechanical decision and time; prior exposure refs; linked Execution ref for code-only correction when available. | 008/controller. The assessment is separate from the immutable Plan payload so it cannot rewrite the plan. |
| `ExposureRecordV1` | Inquiry and Plan refs; actor ref; material seen; source or partition ref; occurred-at time; basis (`observed`, `self_reported`, `unknown`); evidence refs; host guarantee; reason/limits. | 008 records it before result delivery and commits it via 009. Records chain in committed event order. |
| `CommittedInquiryHistoryV1` | Inquiry ref, committed manifest ref/revision, ordered Question/Plan/Change/Exposure/execution refs and envelopes, history-completeness indicator, read time and source guarantee. | Supplied by controller through its committed-read capability after 009 recovery. 008 validates identity and derives a worker-safe projection; it cannot add uncommitted records. |
| `PlanExposureContextV1` | Original wording/interpretations; current and prior Plan refs and immutable labels; relevant Exposure timeline and refs; source/partition boundaries; known inspection policy; history-completeness and host guarantee; context digest. | Built for each worker invocation by the controller with 008. On restart it is rebuilt from committed history rather than the worker's summary. |

## Relevant Plan identity fields

The mechanical substantive-change comparison covers the FR-008 dimensions: population, target, method, partition design, search space, and stopping rule. It also compares the profile's locked comparison definition and any field that changes the same analytical comparison. A whole-payload digest detects content changes, while the field comparison explains the category. The Plan's author/time/ref and code digest do not by themselves change analytical identity.

An exploratory Plan has only the shared registration fields and is not assigned a directed confirmation label. A directed registration must validate every mode-specific field in the frozen schema. Predictive candidate/model search and resampling declarations stay bound to the exact locked candidate family, split and tuning design, resampling scheme, seed rule, repeated-evaluation rule, and stopping condition.

## State derivation

| Evidence at the relevant boundary | Derived interpretation |
| --- | --- |
| No directed lock exists; analysis is bounded exploration | `exploratory` |
| Lock and inspection policy predate relevant outcome access; committed history is complete; the relevant partition boundary and guarantee are explicit | `planned_confirmatory` |
| Relevant outcome exposure is known before a substantive Plan fork or before an unplanned choice | `outcome_informed` |
| Required history is missing/corrupt, or prior access cannot be observed or ruled out under the actual host guarantee | `exposure_unknown` |

The status is recomputed for a new Plan from lock-time rules and all applicable committed exposure. The value stored on any previous Plan is never changed. A code-only repair keeps its Plan identity, creates a new Execution/code digest, and records the outcomes already seen. Human approval or risk acceptance may authorize exploratory use but never changes a historical Plan label.

## Exposure facts and ordering

`material` names what the actor could inspect (for example schema, predictor values, labels, aggregate, evaluation metric, prior claim, or outcome-bearing skill summary). `sourceOrPartitionRef` points to the exact source/data version or partition when applicable. Actor, time, evidence, and observation basis are mandatory; where any access dimension is unknown, retain an explicit unknown with a reason rather than substituting “none.”

The controller commits an exposure record before placing its corresponding material in a worker context. A loaded skill or shared summary containing an earlier result creates an exposure event linked to that result's original source/partition. A record of no tool observation is not evidence of no out-of-tool access.

## Relationships and immutability

```text
Question 1 ──* Interpretation
    │
    ├──* Plan revision ──* PlanChangeAssessment
    │       │
    │       ├──* Exposure refs at lock
    │       └──* Execution (including new code-only execution)
    └──* ExposureRecord ──* prior result / source / partition / skill refs

CommittedInquiryHistory ──> PlanExposureContext ──> current or replacement worker
```

The graph is a readable relationship sketch, not a new graph database. Every reference resolves through a committed run manifest and is validated by stable ID, revision, and digest. Revisions are append-only; no review, restart, new harness, summary, or approval may mutate prior payloads, exposure events, or stored interpretations.
