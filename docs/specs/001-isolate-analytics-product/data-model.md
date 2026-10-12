# Data Model: Boundary, Principals, and Contracts

These are wire and boundary records for feature 001. They do not create
committed analytical Results or Claims.

## Principal

Represents the authenticated actor for a product request.

| Field | Type | Rule |
| --- | --- | --- |
| `schemaVersion` | literal `1` | Required at serialized ingress. |
| `principalId` | nonempty string | Comes from the local authenticated CLI/session identity, never from worker-authored input. |
| `authenticationMethod` | nonempty string | Names the configured local authentication source. |
| `roles` | string array | Resolved from project configuration; worker cannot add a role. |
| `projectId` | nonempty string | Must equal the target project on the request. |

## SourceGrant

Represents the configured authority to perform named operations on one source.

| Field | Type | Rule |
| --- | --- | --- |
| `schemaVersion` | literal `1` | Required. |
| `grantId` | nonempty string | Stable configured grant identity. |
| `principalId` / `roleId` | exactly one selector | Grant applies to a principal or a configured role. |
| `sourceId` | nonempty string | Exact source identity; no wildcard source expansion. |
| `operations` | enum array | `metadata`, `aggregate`, `row`, `sheet`, `export`, or `administration`. |
| `audience` | nonempty string or configured set | Must match the request audience. |
| `effectiveFrom`, `effectiveUntil` | timezone-aware timestamps | Current time must be in the half-open configured interval. |
| `ownerRef` | `RecordRefV1` | Identifies grant authority. |
| `revision` | positive integer | A changed grant creates a new version. |

No matching active grant, missing principal, role mismatch, project mismatch,
unknown operation, or wrong audience denies. A worker cannot create or widen a
grant.

## AccessDecision

An operation result is attributed to a principal and exact source/operation.
It records `allowed` or `denied`, reason code, matching grant reference when
allowed, time, request surface (`controller`, `source`, `catalog`, or
`viewer`), and the guarantee label. Protected callbacks are not entered on a
deny. The decision records access control only; it does not mean a source
assertion or analytical result is valid.

## WorkerContextPacket

One immutable input record for a worker invocation: `contextId`, original
question, product identity, references to applicable analytics instructions
and policy contracts, access-filtered source/catalog references, grant refs,
prior exposure refs when supplied, allowed worker-workspace files with
digests, requested outputs, principal ref, harness/model/version, creation
time, and packet digest. It carries an explicit guarantee label. No generic
factory prompts, task templates, repository-wide search results, evaluator
answers, or ungranted references are included.

The compiler only emits references already authorized for that principal and
audience. The same source identity and operation checks are repeated at actual
read callbacks.

## Neutral task and execution

`NeutralTaskV1` declares task ID, dependency IDs, executable, argv, working
directory, allowed output root, timeout, silence limit, and environment
overrides. `NeutralRunOptionsV1` declares run-file identity, concurrency,
retries, maximum attempts, review/repair rounds, fallback chain, and final
review mode. The analytics adapter fixes the latter controls to retries `0`,
maximum attempts `1`, review/repair rounds `0`, empty fallback, and final
review `off`.

`NeutralExecutionV1` records task/attempt IDs, times, process status and
failure kind, exit code, invocation digest, stdout/stderr log references, and
produced paths. These fields describe execution only; no field implies check
pass, supported Claim, successful inference, or answered inquiry.

## PlanRegistrationV1

Four strict registration payloads share `schemaVersion`, `planId`, positive
revision, question reference, predecessor reference or null, author reference,
purpose, source bindings, declared output contracts, and budget reference.

- `ExploratoryPlanV1`: common fields only. It adds no invented target or
  confirmatory interpretation.
- `DescriptivePlanV1`: common plus directed fields, with measurements,
  definitions, units, denominator, exclusions/missingness, grouping, and
  population/period scope.
- `PredictivePlanV1`: common plus directed fields and baseline/candidate,
  feature definitions, prediction time/availability, target horizon and
  maturity, evaluation population, split/tuning separation, locked search,
  primary metric/direction/delta, interval, resampling, seed, and repeated
  evaluation procedure.
- `InferentialPlanV1`: common plus directed fields and the initial
  `two_group_renewal_rate_difference` profile, customer observation unit,
  exactly two group definitions, renewal denominator/period, estimand,
  practical margin, sampling/missingness/dependence assumptions, interval,
  comparison family, multiplicity, and inspection rule.

Every model rejects undeclared keys and non-finite values. Unsupported
profiles remain explicit limitations, not generic schema matches.

## AnalyticsRequestV1 and AnalyticsResultV1

`AnalyticsRequestV1` contains `schemaVersion: 1`, request ID, mode
(`descriptive`, `predictive`, or `inferential`), exact computation binding,
mode-specific payload, and bounded output declarations. The binding includes
run, inquiry, attempt, execution, Plan revision, source and transformation
references, code digest, Python version, dependency-lock digest, environment
digest, and seed.

`AnalyticsResultV1` echoes request ID, mode, exact binding, typed mode result,
produced artifact IDs/paths/digests/media type/size, and completion time. A
future calculator must echo every binding exactly. Feature 001 validates the
envelope and dispatch path only; absent calculators return unsupported-mode
errors without dummy results.

## Local boundary demonstration

The acceptance artifact records one demo ID, original question, exact context
digest and permitted-file list, principal/grant refs, guarantee label,
neutral run/task/execution IDs, attempt count, process exit/status/log refs,
and the independent failed-check/negative-result fixture states. It contains
no protected raw rows and makes no analytical-support assertion.
