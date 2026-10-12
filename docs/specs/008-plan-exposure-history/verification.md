# Feature 008 Verification Record

**Status**: Planned; implementation evidence has not been produced in this task-plan stage.

**Feature**: [Preserve Plans and Outcome Exposure](spec.md)  
**Plan**: [plan.md](plan.md)  
**Acceptance owner**: `src/analytics/acceptance/008.test.ts`  
**Synthetic sources**: Feature 18-bootstrap registry only  
**Execution and publication**: Feature 009; full worker-context integration: downstream feature 003

## Commands to run after implementation

| Unit | Commands | Expected use |
| --- | --- | --- |
| `mode-registration` | `pnpm typecheck`; `pnpm test` | Compile the facade and execute valid-profile plus strict-rejection assertions. |
| `locked-revision-exposure` | `pnpm typecheck`; `pnpm test` | Compile Plan/exposure transitions and verify durable commit gating, release permits, and no stage invocation after rejection/conflict. |
| `facade-rehydration-acceptance` | `pnpm test` | Run the complete 008 acceptance suite against the production facade and 009 stage/persistence services. |

No implementation or product-test command was run while producing this planning record.

## Functional Requirements

| Requirement | Planned executable evidence | Result |
| --- | --- | --- |
| FR-001 | Facade acceptance preserves original wording, all operational interpretations, and each immutable Plan revision in `src/analytics/acceptance/008.test.ts`. | Pending implementation |
| FR-002 | `registerPlanV1` validates each supported generated profile and the directed fields; rejection cases cover unsupported profile, mismatched discriminator, missing/extra fields, bad refs, and non-finite values. | Pending implementation |
| FR-003 | `commitExposureBeforeReleaseV1` asserts actor, material, source/partition, time, evidence, and observed/self-reported/unknown basis and access guarantee. | Pending implementation |
| FR-004 | Outcome-informed substantive fork receives a new Plan ref, predecessor link, prior exposure refs, and new Execution without mutating earlier records. | Pending implementation |
| FR-005 | Declared candidate/resample remains in the locked family; an undeclared post-result choice is exposed and classified separately. | Pending implementation |
| FR-006 | Review approval leaves stored exposure and historical interpretation unchanged. | Pending implementation |
| FR-007 | `rehydratePlanExposureV1` preserves wording, labels, Plan/exposure refs, completeness and budget binding independent of a shortened worker summary. Actual worker delivery is a 003 assertion. | Pending implementation |
| FR-008 | Seeded changes to population, target, method/comparison, partition design, search space, and stopping rule fork; a code-only correction creates a new Execution/code digest without changing Plan identity. | Pending implementation |
| FR-009 | Controller-owned classification is mechanically checked against changed locked fields while worker rationale remains attributed; unresolved semantic classification cannot be `planned_confirmatory`. | Pending implementation |

## Success Criteria

| Criterion | Planned executable evidence | Result |
| --- | --- | --- |
| SC-001 | Injected outcome-informed revision receives a distinct Plan identity linked to earlier Plan/exposure refs. | Pending implementation |
| SC-002 | Every declared candidate/resample stays in the locked family and produces zero false post-observation classifications. | Pending implementation |
| SC-003 | Approval and 008 rehydration cannot remove exposure or change a stored label; 003 separately verifies actual replacement-worker context delivery. | Pending implementation |
| SC-004 | Each seeded substantive field change forks, while code-only correction produces a new Execution and unchanged Plan. | Pending implementation |
| SC-005 | Locked-before-exposure, unplanned, post-observation, and unknown-history fixtures derive respectively `planned_confirmatory`, `exploratory`, `outcome_informed`, and `exposure_unknown`; no approval/restart laundering. | Pending implementation |
| SC-006 | The fixture inquiry retains wording/interpretations, a directed Plan has every FR-002 field, and every exposure contains the required provenance. | Pending implementation |

## Edge Cases

| Edge ID | Planned assertion | Owner/check | Result |
| --- | --- | --- | --- |
| EC-001 | Outcome-bearing skill is represented as exposure with source/partition and prior-result provenance before the release permit is returned. 003 verifies actual context delivery. | FR-003; T25; `src/analytics/acceptance/008.test.ts` | Pending implementation |
| EC-002 | Changed program digest with unchanged analytical comparison creates a new Execution without a false Plan rewrite. | FR-008; SC-004; `src/analytics/acceptance/008.test.ts` | Pending implementation |
| EC-003 | Rehydration from a shortened-summary request retains committed exposure and labels; missing history produces `exposure_unknown`. 003 verifies delivery to a different harness/replacement worker. | FR-007; SC-003; T61; `src/analytics/acceptance/008.test.ts` | Pending implementation |
| EC-004 | Human approval for exploratory use cannot restore a confirmatory label after observation. | FR-006; SC-003; `src/analytics/acceptance/008.test.ts` | Pending implementation |

## Proposal Cases

| Case | Planned 008 evidence | Result |
| --- | --- | --- |
| T06 | Production facade creates the outcome-informed Plan/Execution fork and retains exposure; approval invariance is asserted. Full inquiry path remains with 003. | Pending implementation |
| T07 | Locked search/resampling declaration accepts a declared next candidate after an earlier result without a post-hoc label. | Pending implementation |
| T09 | Early evaluation-metric exposure is committed before release; rejected/conflicting commits yield no permit and zero `executeStageV1` calls. Out-of-tool access remains unknown; 003 verifies actual delivery. | Pending implementation |
| T61 | 008 rehydration result retains committed exposure and the post-observation Plan label despite a shortened summary. 003 owns the full replacement-worker context assertion. | Pending implementation |
| T25 (edge provenance) | Outcome-bearing skill/shared summary preserves source and evaluation exposure provenance. Primary case owner remains feature 015. | Pending implementation |

## Controller and Trust Boundary

The 008 acceptance calls the production `plans.ts`/`exposure.ts` facade hooks with synthetic/local fixtures and uses 009's actual commit/recovery and `executeStageV1` services. It does not build a test controller, require a live delegated ask, or claim that a rehydration projection has reached a worker. A `rejected` or `revision_conflict` exposure commit returns a blocked result without a release permit; the acceptance asserts that the stage coordinator is not called. Feature 003 consumes the same request/result hooks and tests actual outcome delivery, worker replacement, and the user-visible inquiry timeline through its production controller/CLI.

Local evaluation partitions readable by a worker shell must display `workflow-withheld; access isolation not enforced`. Unknown or unobserved out-of-tool access remains unknown. The 008 facade test establishes its tested application/publication boundary only; it does not prove tamper-proof storage, universal semantic correctness, enforced holdout isolation, or full 003 integration.

## Evidence to attach during implementation

- Actual `pnpm typecheck` and `pnpm test` invocations, exit codes, and test counts.
- Synthetic fixture IDs and registry digests used by the run.
- Acceptance assertion names and committed Plan/Exposure refs for every row above.
- 009 publication evidence for committed/replayed exposure, plus rejected/conflicting outcomes showing no release permit and zero stage invocations.
- Recovery evidence that orphan/uncommitted refs are excluded and unknown/corrupt history is not interpreted as no exposure.
- Record the downstream 003 integration as pending if it has not run; when available, link its controller/CLI trace for actual replacement-worker context delivery.
