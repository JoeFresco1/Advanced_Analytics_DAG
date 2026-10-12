# Required Checks Contract (Feature 010)

This feature consumes the version-1 record and execution contracts frozen in
`docs/build/interfaces.md`. In particular, `CheckResultV1` field names and
outcome values are not redefined here. This document describes the policy and
service boundaries that `checks.ts` and `policies.ts` implement.

## Policy registration boundary

`policies.ts` accepts a proposed profile only through the configured owner
registration route. Before returning a usable policy reference, it validates
the strict profile shape, applicable method and scope, required validators,
validator ID/version/code references, parameter contracts, permitted
`not_applicable` reasons, claim-presentation rule, and authority route. It
executes the profile's policy-owned fixtures. Failed schema or fixture checks
leave the proposal unregistered. The registration result records the owner
and immutable policy `RecordRefV1`.

Worker-authored profiles and check proposals remain attributed proposals.
They cannot become current policy or replace a required validator. A policy
change creates a new revision/reference. Existing Check Results remain bound
to their original policy, and affected Claims are reassessed against the new
version.

## Parameter admission

Before execution, the controller resolves each required validator from the
registered policy and admits only parameters described by that validator's
frozen schema/constraints. Reject missing required parameters, unknown keys,
unsupported values, out-of-range values, and any worker attempt to choose a
validator, command, code version, required condition, N/A allowance, approval
authority, or presentation criterion. Canonicalize and digest the admitted
parameters; bind that digest to the execution evidence.

Additional worker-proposed checks may be considered as additions under the
controller's admission rules. They cannot remove, rename, replace, weaken, or
alter the required set.

## Execution and result ingestion

1. Resolve the current Plan, policy, validator and code references, and exact
   subjects before launching the check.
2. Invoke the policy-owned executable through 009 `executeStageV1` with an
   argument vector and declared output schema/path. Do not construct shell
   text from worker parameters.
3. Retain the neutral execution record independently from the analytical
   outcome. A zero exit may yield `fail`, `inconclusive`, or
   `not_applicable`; it does not imply `pass`.
4. Validate output schema and every binding against the request. Persist a
   well-formed `CheckResultV1` using the shared frozen fields.
5. A checker crash, unexpected process failure, missing/malformed result
   envelope, or output-binding mismatch is an execution/evidence error and
   cannot yield pass. Record `outcome: error` with the known exact request
   bindings and reason when a controller result can be formed; retain raw
   malformed output as a diagnostic if available.
6. Never retry analytical work because an outcome is fail, inconclusive, or
   a valid negative observation. The runner's generic retry/review paths
   remain disabled; a separately classified infrastructure recovery is
   recorded by the neutral execution contract and does not change the
   analytical result.

## Coverage and truth table

Coverage is computed from the complete required validator set in the exact
policy version, not from the worker's submitted result list. Each result must
bind to the expected policy, Plan, validator/version/code and exact subjects.
An absent condition is `missing`. A prior result with a changed subject,
policy, validator/code or Plan binding is `stale`. These are coverage states,
not Check Result outcomes; historical records remain unchanged.

| Current required condition | Eligibility contribution |
| --- | --- |
| `pass` | Satisfied |
| `not_applicable` with exact policy-supported reason | Satisfied as not applicable; never relabeled pass |
| `fail` | Blocks |
| `error` | Blocks |
| `inconclusive` | Blocks |
| `missing` coverage | Blocks |
| `stale` coverage | Blocks |
| `not_applicable` without a policy-supported reason | Blocks |
| No registered policy or unsupported method/scope | Blocks; do not synthesize a permissive N/A |

The controller sets `supported_presentation_eligible` true only when every
required condition contributes satisfied. It retains per-condition reason
and references so the viewer can explain the decision. Risk acceptance,
review, process success, or a worker override cannot change this truth table.

## Tested failure classes and limits

Every passing Check Result declares its tested failure classes and limits.
The summary must not generalize beyond the validator's actual fixture set.
For example, an entity-overlap fixture establishes that its tested identity
intersection was detected; it does not by itself establish the absence of
temporal leakage, preprocessing leakage, or semantic overlap.

## Primary consumers

Feature 011 reads these immutable records in the claim trace. Integration
feature 002 exercises the fixed-analysis producer, required-check consumer,
publication path, and browser viewer with pass, overlap-fail, malformed, and
stale/missing variants. Those consumers do not redefine the feature 010
policy or eligibility rules.
