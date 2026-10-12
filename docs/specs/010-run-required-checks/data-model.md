# Data Model: Required Checks

The record identities, references, and `CheckResultV1` fields in this feature
are governed by `docs/build/interfaces.md`; machine values are governed by
`docs/specs/state-vocabularies.md`. This model describes their feature-specific
relationships and validation rules without creating a competing wire schema.

## Records and values

### Check policy profile

An immutable, versioned project record registered by the configured owner
before dependent execution. Its declared content includes:

- Stable profile ID, revision, digest, effective method/profile and scope.
- Required validator IDs and exact validator versions/code references.
- Parameter schema and bounds for each required validator.
- Explicit permitted reasons for `not_applicable`.
- Claim-presentation rule and configured authority route.
- Registration actor/time and policy-fixture validation evidence.

Policy identity is a shared `RecordRefV1`. A changed profile is a new version.
An unregistered profile or unsupported method does not have applicable
required-check coverage and cannot make a Claim eligible.

### Validator descriptor

An immutable policy-owned binding from validator ID/version to executable
code reference, accepted parameter contract, declared output schema, tested
failure classes, and known limits. Validator command and code identity come
from the approved profile/registry. Worker input can supply only admitted
parameters and cannot substitute an implementation.

### Admitted check execution

One required validator invocation bound to its execution ID, exact Plan and
policy references, canonical parameter digest, and exact subject references.
The neutral execution record carries process status and diagnostic logs;
those fields do not encode analytical support.

### Check Result (`CheckResultV1`)

The shared frozen record contains `schemaVersion`, `checkId`, `executionId`,
`planRef`, `policyRef`, validator ID/version/code reference, exact subject
references, canonical outcome, observed and expected values, evidence
references, tested failure classes and limits, and reason. Its outcome is
exactly one of `pass`, `fail`, `inconclusive`, `not_applicable`, or `error`.

The validator's analytical result is distinct from command status. A
successfully completed overlap validator can have process success and
`outcome: fail`. An execution or evidence problem such as a crash or malformed
result envelope is recorded as `error` with its reason; its process record is
also retained. Missing validator output is never `not_applicable`.

### Required-check coverage

Coverage is assessed against the validator set declared in the exact policy
revision and each required validator's exact subject set. The coverage
assessment may report `missing` when no corresponding result exists and
`stale` when a result's policy, validator/code, Plan, subject identity,
revision, or digest no longer matches. These values are coverage findings,
not Check Result outcomes. The historical Check Result remains unchanged.

### Supported-presentation eligibility

For each dependent Claim, the controller evaluates the Claim's exact Result
and evidence revision set against the current policy and coverage. The
computed `supported_presentation_eligible` boolean is true only if every
required condition is current `pass` or policy-supported `not_applicable`.
The latter stays visibly `not_applicable`. Any fail, error, inconclusive,
missing, stale, unsupported scope, or unjustified N/A blocks eligibility.
Human review or risk acceptance does not change the computed check outcome or
Claim evidence state.

## Relationships

```text
Configured owner
  └── registers immutable Check Policy version
        ├── declares required Validator versions + parameter contracts
        └── is pinned by Plan revision
              └── admits Check execution
                    ├── runs through 009 stage coordinator
                    ├── binds exact subject refs + admitted parameter digest
                    └── produces immutable CheckResultV1
                          └── coverage assessment for dependent Claim
                                └── supported_presentation_eligible
```

## Validation and history rules

1. Policy schema and its declared fixtures pass before the policy is effective.
2. Worker-supplied unknown parameters, out-of-bound values, validator IDs,
   versions, commands, scopes, and release rules are rejected before run.
3. An N/A satisfies a required condition only if the frozen policy version
   lists its exact reason; otherwise it blocks while retaining the N/A record.
4. Old subject or policy bindings are stale coverage even if the old validator
   outcome was pass. They are never rewritten to match a new revision.
5. Successful negative analytical outcomes are not infrastructure failures
   and do not prompt retries seeking a pass.
6. Results and diagnostics are published using 009's append-only commit and
   recovery service. A partially written record is not reader-visible.
7. Validator summaries state only the failure classes they tested and the
   limits recorded in their frozen descriptor.
