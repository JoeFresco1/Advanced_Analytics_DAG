# Data Model: Execute and Inspect Evidence

This integration consumes the canonical records and does not introduce a new analytics state vocabulary or alternate record schemas. Stable references carry record ID, revision, and SHA-256 digest. Artifact evidence adds artifact ID and current availability; a path alone is never an identity.

## Fixed fixture and Plan

- The demo scenario name is a test-fixture selector: pass, overlap-fail, null-improvement, malformed-check, checker-exit, missing-output, changed-artifact, concurrent-publication, pre-commit-recovery, acknowledgement-replay, inaccessible-evidence, or redacted-evidence. These names select deterministic fixture behavior; they are not domain state values.
- The plan is one immutable, schema-valid PredictivePlanV1 member of PlanRegistrationV1 from the generated 001 Pydantic contract. It has a stable Plan ref, revision, and digest. The execution binds that exact ref. 002 does not implement Plan registration, locking transitions, or plan interpretation.
- Synthetic source and transformation references identify the fixed fixture version and deterministic preparation steps. Their scope and produced artifact IDs remain in the trace; 002 does not own the general source/measurement facade.

## Execution and computation

- StageRequestV1 binds stage identity, principal, execution and attempt IDs, Plan/source/transformation refs, program and environment digests, executable/argv, timeout, and declared output contract.
- executeStageV1 returns StageResultV1 with the exact execution ref, output refs, and publication status. Process completion remains independent from analytical outcome.
- AnalyticsRequestV1 and AnalyticsResultV1 use the shared schemaVersion 1 envelope and mode-specific Pydantic payload. The result echoes the complete computation binding and names staged ProducedArtifactV1 outputs with digest, media type, size, and relative path.
- Result selectors bind visible numeric values to their exact structured result, unit, denominator, and scope as required by 011.

## Policy, Check Result, and Claim

- The method policy is owner-registered and immutable for the run. Its RecordRefV1 supplies the policy revision and digest; the worker cannot remove required checks or permit an unsupported not_applicable reason.
- CheckResultV1 identifies check/execution/Plan/policy refs, validator ID/version/code ref, exact subject artifact refs and digests, canonical outcome, observed/expected values, evidence refs, failure classes/limits, and reason.
- A successful validator process may produce outcome fail. A crash, malformed/missing envelope, unexpected exit, or wrong subject produces error or stale/missing coverage through 010's rules.
- Claim evidence state remains one of supported, unsupported, inconclusive, or conflicting. supported_presentation_eligible is a separate boolean computed by 010 for the exact Claim, Result, check, artifact, validator, and policy versions. A blocked Claim and its Result/Check remain inspectable.

## Publication and recovery

- CommitRequestV1 names the stable commit ID, run ID, expected manifest revision, principal, execution/attempt refs, immutable records/artifacts, validation decision refs, and commit time.
- CommitResultV1 status is committed, replayed, revision_conflict, or rejected. The manifest pointer is the visibility boundary; uncommitted staging is not reader evidence.
- RecoveryReportV1 names the committed head, replayed IDs, ignored staging/orphans, unresolved/corrupt refs, and safe cleanup actions. A pre-commit interruption cannot promote staged bytes. Replaying the same ID/content returns the existing revision; an ID collision with changed content is rejected.
- Reproduction state uses exactly not_attempted, reproduced, mismatched, or unavailable with the declared byte, canonical-value, or numerical-tolerance comparison. A value-preserving serialization change is not reclassified as a numerical mismatch.
- Evidence availability uses available, unavailable, or redacted. Losing access preserves an access-safe historical pointer and visible reason.

## User trace

A report assertion opens the exact Claim and Result selector, Check Results/eligibility, computation/attempt, Plan and policy refs, transformation/source versions, and committed artifact IDs. 011 resolves IDs through committed manifests and checks viewer access again. A denied reader receives only permitted metadata and limitations; the UI cannot treat a worker's prior grant as browser authorization.

## Evidence relationships

Plan + source version + transformation refs + program/environment -> Execution/Computation -> staged outputs -> 009 committed Result/artifact refs -> 010 exact Check Results and eligibility -> 011 bound report assertion and browser trace.

A later availability revision, artifact change, or policy/result change does not rewrite the earlier evidence; it makes the current link unavailable/stale or requires reassessment under the owning feature contract.

