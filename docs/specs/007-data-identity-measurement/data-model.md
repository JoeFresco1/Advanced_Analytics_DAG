# Data Model: Data Identity and Measurement

This feature adds domain records around the frozen shared RecordRefV1 and descriptive computation envelopes. Every committed version is immutable. Corrections and meaning changes create a new version with a predecessor/correction link; they do not rewrite old records.

## Shared references and authorities

| Reference | Meaning and owner |
| --- | --- |
| RecordRefV1 | Stable record ID, revision, and content digest from the shared interface contract. A path or display name is not identity. |
| Principal and SourceGrant | Authenticated actor and configured access grant owned/enforced by 001. Feature 007 stores exact grant references and invokes the 001 check before permitted reads; it cannot create roles or privileges. |
| PlanRegistrationV1 | Immutable plan registration owned by 001/008. Descriptive measurements provide definition, statistic, units, denominator, missingness/exclusion rule, group meanings, population, and period. |
| ComputationBindingV1 | Exact plan, source, transformation, program, environment, and execution identity owned by the common computation protocol. |
| CheckResultV1 and global eligibility | Frozen check record and supported_presentation_eligible assessment owned by 010. Feature 007 emits measurement findings that the controller maps through this check path. |

## Entities

### Source identity and data version

A source identity describes the logical source without credentials. Each acquisition or import creates an immutable source/data-version record with:

- source ID and exact RecordRefV1; content digest or source-native version evidence where available;
- declared source role, kept distinct from a principal's project/access role;
- exact Principal/SourceGrant references, permitted operation, audience/scope, and effective interval as observed at acquisition;
- retrieval scope, acquired time, and source-specific consistency statement with its evidence;
- exact source snapshot/native version reference when actually provided; never derive one from a query timestamp;
- schema reference plus semantic references for entity keys, units/currency, timezone, category definitions, and business meanings;
- observation window, event-time/processing-time definitions, watermark/freshness facts where present;
- current availability and source-version replay availability with reason;
- explicit unknown values for historical origin, consistency, grain, cardinality, definitions, or freshness that the source did not supply.

Source role answers which analytical part the source represents; a grant answers which operation the principal may perform. Both must match the plan and source adapter. A mismatch is recorded and denied or support-blocking before protected rows are exposed. Source availability and reproducibility are separate: an unavailable original may still have permitted derived evidence, but cannot be described as replayable.

### Transformation declaration

Before execution, an immutable declaration pins:

- exact input version refs and expected input grain/entity keys;
- expected key/join cardinality and the declared output grain;
- whether row expansion or a grain change is intentional and its denominator meaning;
- joins, expected unmatched behavior, time cutoffs, population filters and missingness/exclusion rules;
- units, timezone and category definitions required for the output;
- freshness expectation and, when labels/targets are used, maturity rule and target horizon.

A transformation consuming imported input with unknown historical grain/cardinality must declare its expected input grain and key cardinality before it executes. It cannot fill those facts into the old source record.

### Transformation manifest

The post-execution immutable manifest references its declaration and records:

- exact input refs, output data-version ref, execution/program/code/environment digests;
- observed input/output grain and entity keys;
- Python-produced row counts, distinct entity counts, join multiplicity/unmatched counts, and per-reason exclusion counts;
- actual joins, filters, missingness, unit/category/time/maturity treatment and resulting population scope;
- producing execution and timestamp.

Observed and declared values stay separate. A missing declaration or a mismatch is a finding. A higher row count is not automatically invalid: for a declared entity-to-event one-to-many transformation, repeated entity rows are allowed when the output grain is explicit and the distinct-entity denominator reconciles.

### Measurement scope

Every named descriptive measurement references:

- statistic: count, sum, mean, or proportion;
- exact measurement/column definition and units;
- population and period refs with boundary/timezone semantics;
- denominator-definition ref and denominator unit (row, entity, event, or another explicitly defined observation);
- missingness and exclusion rules, with observed missing and excluded counts/reasons;
- group/category definition refs and requested group scope, if grouped;
- maturity/freshness expectations and exact transformation/source path.

An unknown group meaning does not become a guessed label. Raw category keys may be retained as observed values when access permits, but their semantic grouped claim is support-blocked.

### Denominator lineage

A denominator lineage starts at its source population and contains the exact ordered transformation refs to the final measurement. At each step it records the declared and observed grain, input/output row and entity counts, join effects, unmatched keys, exclusion counts/reasons, and resulting denominator count/definition. The final result points to this chain. Count conservation is assessed in the denominator's declared entity/event unit; row equality is not required.

Any nonzero change in the final denominator must resolve to a declared population/period change, join effect, or named exclusions. A hidden filter or unexplained change produces the owning check finding and a support blocker for that measurement. A valid declared one-to-many join can have more rows and an unchanged entity denominator.

### Maturity and freshness

For time-dependent inputs, store source window, event time, processing time, watermark/late-arrival information, freshness observation time, and the versioned expected freshness rule when supplied. Do not invent a global freshness threshold.

For target labels, store target definition/horizon, cutoff, maturity rule reference, and eligible versus immature counts by affected cohort/group. Immature labels are excluded with reason/count or marked unavailable. They are not counted as negative outcomes and cannot silently enter a supported denominator. Late arrivals retain original event time and source-version provenance and create a new data/window version when they alter previously measured data.

### Measurement finding

A finding contains its exact check ID/version, result and subjects, canonical check outcome, observed/expected evidence, evidence refs, reason/limits, and a boolean indicating whether it blocks dependent measurement support. Check IDs identify the measurement owner, including source-role/version, grain/cardinality, denominator, semantic-definition, freshness, group-meaning, and label-maturity checks.

A finding is calculation evidence, not the global CheckResultV1, Claim evidence state, presentation-eligibility decision, or authorization. Controller 003 passes required findings to the frozen 010 policy/check consumer. A valid negative or inconclusive finding is retained; it does not trigger a favorable-result retry.

### Descriptive result

Python computes each statistic from the exact input. Each result retains its named measurement, nullable finite value, units, population/period/group scope, numerator where applicable, denominator count and definition, missingness/exclusion counts and reasons, and denominator lineage refs. An undefined proportion or mean with denominator zero has denominator 0 and no numeric value, plus an explanatory reason; it is not reported as 0, NaN, or infinity. TypeScript returns the Python result after exact binding/schema validation and does not calculate the statistic.

## Relationships

- One logical source may have many immutable acquisitions/data versions.
- One transformation declaration produces at most one committed transformation manifest for an admitted execution; a new execution creates a new manifest/version.
- A transformation consumes exact source or transformation output refs and produces a new output version.
- A measurement uses a declared ordered transformation path and one denominator definition.
- A grouped measurement uses exact category/group meaning refs for each category definition.
- Findings bind the measurement/result and exact transformation/data refs they evaluate.
- A source semantic revision invalidates unqualified current comparisons that depend on that meaning while preserving all historical records.

