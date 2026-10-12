# Research: Frozen Policies and Structured Required Checks

This feature has no unresolved technology or product-policy questions. The
accepted feature spec, proposal revision 2.6, canonical state vocabulary, access
and judgment contract, and the 2026-09-28 shared build interfaces settle the
choices below. No external research or research agents were needed; the task
directive requires resolving this bounded plan from the approved local
contracts.

## Decisions

### Controller and validator execution

**Decision**: Implement policy admission and check handling in TypeScript under
`src/analytics/`. Invoke required validator commands through 009's frozen
`executeStageV1` service with explicit argument vectors and declared output
schemas.

**Rationale**: `docs/build/ARCHITECTURE.md`, `docs/build/interfaces.md`, and the
build graph assign the analytics controller and the `CheckResultV1` owner to
TypeScript feature 010, while 009 owns the single-stage coordinator. The
proposal requires checks to be explicit executable tasks and forbids a second
runner.

**Alternatives considered**: Running a validator through an ad hoc shell call
would bypass the execution binding and single runner. Treating an in-process
worker callback as the policy authority would allow the submitting worker to
replace required behavior. Neither is used.

### Policy authority and versioning

**Decision**: A configured project owner registers an immutable, versioned
policy profile only after strict schema and policy-owned fixture validation.
Worker proposals remain proposals. A change creates a new policy reference
and triggers a fresh evaluation of dependent evidence.

**Rationale**: This is required by FR-008 and the access and judgment contract.
It also preserves proposal I06 and the shared interface rule that authority
and release policy are not worker-selected.

**Alternatives considered**: Accepting a worker-authored profile, editing a
profile in place, or making approval itself imply a passing check would violate
the accepted ownership and history rules.

### Outcome, coverage, and eligibility

**Decision**: Keep the canonical outcomes `pass`, `fail`, `inconclusive`,
`not_applicable`, and `error`. Represent absent and outdated required evidence
as coverage `missing` and `stale`, never as validator outcomes. The controller
computes presentation eligibility from current exact bindings and the frozen
policy's explicit N/A reasons.

**Rationale**: This is the canonical state vocabulary and frozen
`CheckResultV1` interface. It also permits an honest failed finding to remain
visible without becoming supported evidence.

**Alternatives considered**: Treating process exit zero as check pass,
collapsing N/A into pass, or allowing human risk acceptance to override
eligibility would erase distinct analytical facts and are rejected.

### Parameters and retries

**Decision**: Admit worker parameters against the policy-owned schema and
constraints, hash the canonical admitted set, and include that digest in
execution evidence. A valid negative result or failed analytical condition
does not trigger retries toward a favorable result.

**Rationale**: The proposal separates integrity evidence, analytical
observations, and use criteria; I04 says a negative result is not a process
failure. Shared execution is configured for one generic attempt.

**Alternatives considered**: Allowing worker-provided command arguments,
silently adding parameters after observing a result, or retrying until the
condition passes would permit policy bypass or outcome selection.

## Fixed assumptions

- Project owner identity and authority routing are supplied by configured
  project roles; a worker cannot create that authority.
- Record references and artifact subjects use the shared stable ID, revision,
  digest, and artifact ID contract.
- The 009 stage and commit/recovery interfaces exist by this feature's graph
  dependency and remain the only execution/publication paths.
- All feature fixtures and demonstrations are synthetic/local.
- The local trust boundary is controller-enforced workflow policy, not a
  tamper-proof boundary against an administrator with repository access.
