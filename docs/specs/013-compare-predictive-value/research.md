# Research: Compare Predictive Value

Planning choices are resolved from the accepted feature specification, the
proposal revision 2.6, the shared architecture, and the frozen interface
contract. No requirement or threshold is changed.

## Decisions

### Keep Pydantic and the 001 lock as the contract and environment authority

**Decision**: Consume AnalyticsRequestV1, PredictiveRequestV1,
AnalyticsResultV1, PredictiveResultV1, the generated JSON schemas, shared
conformance vectors, and the Python package/lock from 001. Do not define a
new predictive wire schema or edit Python dependencies.

**Rationale**: The interface freeze names Pydantic as the normative source
for both Python and TypeScript. A second contract would allow registration,
computation and evidence binding to drift.

**Alternative considered**: Add a TypeScript-owned predictive request or
duplicate Python models for this feature. Rejected because it violates the
shared schema and ownership contract.

### Compare paired values on one locked evaluation population

**Decision**: Fit and score the baseline and candidate on the same
predeclared observation set, split, evaluation time, metric and denominator.
Calculate a paired candidate/baseline gain in the favorable direction.

**Rationale**: A larger displayed score cannot establish added value when the
candidate used a different population or metric. Pairing also preserves the
comparison unit for uncertainty resampling.

**Alternative considered**: Compare separately computed model summaries or
allow each candidate to select its own eligible rows. Rejected because it
does not identify the predictive value added by the candidate under a
comparable evaluation.

### Fit every learned transform inside each training partition

**Decision**: The Python pipeline fits imputers, encoders, scalers, feature
selection and other learned preparation only from the corresponding training
partition/fold; evaluation rows are transformed by the fitted training
object. Tuning and search use only the declared training-side split.

**Rationale**: A globally fitted transformer or evaluation-driven model
selection leaks evaluation information even if row IDs are disjoint.

**Alternative considered**: Fit one preparation object before splitting.
Rejected because it lets held-out distribution information enter the
training procedure.

### Respect the Plan's temporal/group split and dependence contract

**Decision**: Enforce the registered split and its entity/time separation.
Calculate uncertainty only with the interval method and resampling
unit/scheme declared in the locked Plan. Use paired resampling at that
declared independent unit. If the chosen method cannot handle the observed
entity or temporal dependence, fail the required validity condition and leave
the Claim unsupported; do not substitute a row-wise interval.

**Rationale**: Split correctness and interval dependence are related but
distinct. Neither a bootstrap nor a successful command repairs an invalid
design.

**Alternative considered**: Always use a row bootstrap or silently choose an
available interval method. Rejected because that changes the uncertainty
procedure after lock and can overstate precision.

### Separate numerical calculation from post-check Claim classification

**Decision**: Python returns metric values, favorable gain, interval,
denominator, population and the fields required by the frozen Pydantic
PredictiveResultV1. TypeScript verifies exact bindings and consumes current
required-check eligibility before applying the locked delta rule to the
interval. If the strict Python payload also contains a computed class,
TypeScript compares it to the locked calculation but does not treat it as a
supported Claim label until eligibility passes.

**Rationale**: Python is the numerical calculator. The controller is
responsible for required checks and supported presentation. This keeps a
failed validity check from acquiring any Claim-level predictive class.

**Alternative considered**: Treat the Python class or process exit as
authoritative. Rejected because a valid numeric result can still be attached
to an unsupported Claim when required checks fail.

### Treat label maturity as a cohort-specific eligibility condition

**Decision**: A row enters the evaluation only when the target horizon has
elapsed and the label is available by the locked cutoff for that cohort.
Record immature cohorts as failed validity evidence rather than borrowing
maturity from other cohorts.

**Rationale**: Cohort-specific follow-up can make apparently comparable
denominators differ.

**Alternative considered**: Use a single global row count or assume labels
are mature. Rejected because it obscures immature outcomes and changes the
population.

### Keep fixture expectations outside the calculator

**Decision**: Use feature-scoped synthetic fixtures registered by
18-bootstrap. The acceptance file reads their locked reference
classifications/tolerances; predictive code contains no fixture-specific
numbers or output branches.

**Rationale**: Evaluation answers and thresholds are owned by the fixture
registry/evaluator and must remain separate from the worker computation.

**Alternative considered**: Embed expected scores or labels in the
calculator. Rejected because it hard-codes results and invalidates the
demonstration.

### Predeclare metrics and fixture roles

**Decision**: Use binary log loss as the primary metric for the synthetic
churn comparison, with baseline tenure/subscription-plan features and a
candidate that adds recent usage. Use ROC AUC as a separate higher-is-better
directional-control fixture. Predeclare signal, tight below-margin and
low-power null cases for both metric directions; each fixture locks its own
delta and interval settings.

**Rationale**: This gives the concrete analyst scenario one primary
probability-scoring metric while directly exercising both favorable-direction
transforms required by SC-001. The Plan remains the runtime authority; no
metric default or expected score is embedded in the calculator.

**Alternative considered**: Select a metric after looking at fixture scores,
or treat direction as an implementation detail. Rejected because either
choice makes the comparison outcome-informed.

## Resolved unknowns

No open technical decision remains for this slice. This plan relies on the
dependency-owned strict models and on 010's current required-check eligibility
interface; implementation must report an explicit unsupported/error outcome
if an upstream contract is absent or stale rather than inventing a fallback.
