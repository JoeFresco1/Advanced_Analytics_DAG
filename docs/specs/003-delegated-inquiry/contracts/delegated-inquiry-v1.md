# Delegated Inquiry CLI and Extension Contract V1

This feature consumes the shared freeze in docs/build/interfaces.md. This document describes the 003 implementation boundary; it does not version or amend the shared contract.

## Public CLI

The production ask form is:

~~~text
dag analytics ask --project <path> --question <text> --source <fixture> --model gpt-6-luna --reasoning xhigh
~~~

The CLI accepts normal question text and a registered synthetic/local source fixture. It does not accept a user-authored DAG or require the user to choose graph nodes. Intake resolves the authenticated Principal and current grant before source or memory context is exposed. The controller records the original question and the worker's attributed interpretation, intended use, assumptions, and unresolved definitions.

The frozen inspection actions are:

~~~text
dag analytics answer show --inquiry <id> [--open]
dag analytics trace --claim <id>
~~~

Answer show reads committed inquiry/result records, applies the current viewer principal/grant, and can open the feature 011 browser route. Trace follows stable evidence IDs; a displayed numerical assertion resolves to its Result, check/policy, Execution, transformation, and dataset version. Missing, stale, denied, or unsupported links remain visible with safe reasons.

## Worker and stage boundary

The worker receives a controller-built context packet: original question; permitted source and data-version refs; registered Plan and policy refs; prior plan revisions, results, and exposure; remaining reservations; available capability manifest; and exact requested outputs. It returns typed semantic proposals with reason/evidence refs plus authored code/check/report candidates. It cannot commit records, grant access, change a frozen policy, choose the final inquiry outcome, or make opaque calculations authoritative.

Before directed execution, the controller registers and locks a PredictivePlanV1 through 008 using the generated Pydantic-derived contract. Required fields include population/grain, target and denominator, time boundaries, baseline/candidate, comparison, evaluation partitions, search/tuning limits, stopping rule, primary metric/unit/direction, delta, interval and resampling, label maturity, intended use, and check-policy reference.

The controller validates and admits one stage at a time through 012 between settled stages. It executes authorized computation/check work through 009 executeStageV1 with generic retries disabled; uses 007 lineage and 013's Python predictive calculator; and evaluates current required checks under 010. A valid null, negative, or below-margin result is retained and does not cause a favorable-result retry. The local evaluation partition is labeled workflow-withheld; access isolation not enforced.

## AnalyticsExtensionV1

Each provider exports analyticsExtensionV1 from its fixed facade module and contains:

- schemaVersion equal to 1;
- one unique extensionId;
- dependency IDs in dependsOn;
- optional typed commands with exact token paths, argument schemas, required grant operation, and execute(context, parsedArgs);
- optional lifecycle hooks from the frozen set: orient, onAdmitted, afterExecution, afterEvidenceCommit, beforeFinalize.

Command results contain an exit code, access-safe display payload, and committed record refs. Context includes authenticated principal, project/run/inquiry IDs, capability/remaining-budget state, and controller-provided admission, exposure, committed-read, run/project publication and recovery services. Provider code does not receive authority to mint grants or relax policy/budget.

The fixed module/export allowlist and provider IDs are:

| Fixed module | Export | Extension ID | Depends on |
| --- | --- | --- | --- |
| src/analytics/catalog.ts | analyticsExtensionV1 | catalog | none |
| src/analytics/ledger.ts | analyticsExtensionV1 | ledger | catalog |
| src/analytics/retrieval.ts | analyticsExtensionV1 | answers | catalog, ledger |
| src/analytics/source-discovery.ts | analyticsExtensionV1 | source-discovery | catalog |
| src/analytics/skill-refresh.ts | analyticsExtensionV1 | source-reuse | source-discovery |
| src/analytics/recurrence.ts | analyticsExtensionV1 | recurrence | source-reuse |
| src/analytics/authorization.ts | analyticsExtensionV1 | use-authorization | recurrence |
| src/analytics/skill-capture.ts | analyticsExtensionV1 | skill-capture | answers, source-reuse |
| src/analytics/skill-activation.ts | analyticsExtensionV1 | skill-activation | skill-capture |
| src/analytics/inferential.ts | analyticsExtensionV1 | inferential-compute | none |

Descriptive and predictive calculation services are already provided by 007 and 013 to the 003 controller; the listed command-extension loader does not import another calculator or duplicate those services. The allowlist is code-owned and fixed. Input data never supplies an import path or export name.

## Registration and dispatch rules

1. At startup, load only fixed allowlist entries. Record the loaded capability manifest and module/provider digests.
2. An absent exact optional module is an explicit unavailable capability; its commands are unavailable. Only exact optional-module absence may be skipped. Errors from a present module, transitive import, provider export, or schema fail startup.
3. Fail registration on unsupported schemaVersion, duplicate extension ID, duplicate command token path, invalid command argument schema, absent declared dependency, or a dependency cycle.
4. Topologically order providers with a deterministic tie-break based on the fixed allowlist order. Run every lifecycle hook in dependency order. Apply the shared ordering rule of ledger assessment before answer ranking and invoke a ledger match no more than once in orientation.
5. A required provider or hook absent under the active acceptance profile fails that profile's gate. Optional absence remains explicit and does not fabricate capability.
6. Required hook errors halt admission or finalization at the failure point. Keep the error and earlier committed history; do not report completion or suppress the hook as unavailable.
7. On recovery, replay required hooks with stable event/commit IDs. A hook cannot promote unsupported evidence or change grants, budget, policy, or canonical outcome.

## Terminal finalization and durability

The controller derives exactly one terminal inquiry outcome using 012 evidence checks and canonical precedence. It then:

1. Commits a run-scoped terminal intent using a stable ID derived from inquiry, finalization ID, and run scope.
2. Commits the required project-memory records/events referencing that committed run revision with stable project-scoped IDs.
3. Appends the run completion acknowledgement referencing the committed project revision and every required hook acknowledgement.

Same ID and same canonical payload returns the original commit. Reusing an ID with changed content is rejected. Revision conflicts require re-reading the current manifest and an attributed new commit; they never overwrite. After a crash, replay the missing step with the same ID/payload. Until the acknowledgement exists, completion remains incomplete and no completed answer is returned.

## Typed failure outcomes

| Failure | Required behavior |
| --- | --- |
| Missing exact optional module | Publish unavailable capability and prevent dispatch of its commands. |
| Present provider import/export/schema fault | Fail startup and retain diagnostic identity; do not reinterpret as optional absence. |
| Missing dependency, duplicate command, or cycle | Reject registry before command or lifecycle dispatch. |
| Missing/denied principal grant | Stop before protected source, memory, or answer content is included. |
| Invalid Plan, stale check, absent result binding, or failed required check | Retain evidence; block supported presentation of affected Claim. |
| Valid null/non-improvement | Return the scoped analytical outcome without favorable-result retry. |
| Budget stop | Preserve committed evidence and produce a deterministic partial answer within reserved reporting capacity. |
| Required hook or durable write failure | Preserve terminal intent and prior writes; keep completion unacknowledged and replayable. |
