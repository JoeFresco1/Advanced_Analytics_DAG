# Implementation Plan: Isolate the Analytics Product

**Branch**: `001-isolate-analytics-product`  
**Date**: 2026-09-28  
**Spec**: [spec.md](spec.md)

## Summary

Establish the first complete analytics boundary: one authoritative neutral
execution workspace package; an analytics-only, deny-by-default access and
context path; mechanically disabled retry, review redo, repair, and fallback;
strict Python Plan and computation contracts shared with TypeScript; removal
of inherited factory prompts, task surfaces, and default viewer meaning; and a
recorded question-to-neutral-command demonstration whose supplied context and
process outcome are inspectable. Neutral process status remains execution
metadata and never stands in for an analytical check or Claim state.

## Technical Context

**Language/Version**: TypeScript 5.6, Node.js 20+, Python 3.11+  
**Primary Dependencies**: pnpm workspaces; uv; Pydantic 2; NumPy, pandas,
SciPy, and scikit-learn as the locked base for later predictive and inferential
profiles; Ajv validates generated schemas in TypeScript.  
**Storage**: Existing neutral run files and sidecars for execution metadata;
analytics-owned context and demo records are staged under the declared
execution/artifact root. Do not add analytics state to `dag.run.json`.  
**Testing**: `pnpm build`, `pnpm test`, package-level neutral conformance,
`uv run --project python pytest`, shared Python/TypeScript contract vectors,
and the 001 synthetic/local live-worker acceptance.  
**Target Platform**: Local Node and Python runtimes supported by the existing
runner; no remote package publishing.  
**Project Type**: TypeScript controller/CLI and neutral execution package,
with a uv-locked Python worker package.  
**Performance Goals**: No new throughput target; preserve existing neutral
runner behavior and the contract's bounded process timeout.  
**Constraints**: Extra fields, missing required fields, invalid discriminators,
and non-finite numbers fail closed. One neutral process attempt is permitted
for an analytical task. Retry count is zero, maximum attempts is one, review
redo and repair rounds are zero, fallback is empty, and final review is off.
Access enforcement is workflow-level wherever the worker shell can escape its
staged workspace. Build-orchestrator retries/reviews remain outside analytics
runtime policy and all build tooling stays intact. Demonstrations use only
synthetic/local data.

## Constitution Check

| Principle | Plan response | Gate |
| --- | --- | --- |
| Analytics question must reach an inspectable, bounded outcome | `context.ts` compiles and supplies the question to the default worker launch; the live acceptance writes a readable context/outcome record with execution IDs and log/artifact references. | Pass |
| Execution and evidence are different facts | Neutral execution reports process facts only. Failed required-check and valid-negative fixtures remain unchanged and produce no automatic retries. | Pass |
| History and exposure are not rewritten | Each worker start and neutral execution receives its own identifiers and context digest; the demo does not infer support or reset exposure. | Pass |
| Access is scoped and deny-by-default | Principal and SourceGrant are resolved from local identity/project configuration. Controller context, source, catalog, and viewer callbacks all pass through the same production grant gates. | Pass |
| Guarantees match the host | The compiler stages an allowlisted worker workspace. If its shell can reach the parent checkout, the result is labeled `workflow-level`; it never claims filesystem isolation. | Pass |
| End-to-end slices include a producer and inspection path | A configured worker receives a real question, the controller runs a neutral command, and a local inspection artifact exposes the supplied context and process outcome. The browser evidence viewer remains owned by 011. | Pass |
| One authoritative neutral engine | `packages/neutral-execution/` is the sole edited scheduler/process implementation; the legacy adapter is regression-tested against the same vectors. | Pass |
| Build retries are not analytical retries | Keep `scripts/build/`, `.dag-build/`, `docs/build/`, and the active build runner untouched. Only analytics runtime calls through `runner-adapter.ts`. | Pass |

No constitution exception or accepted-contract change is required.

## Design Decisions

1. **Neutral engine ownership.** Extract the shared scheduler/process,
   logs, locking, timeout, artifact-root, stop, and recovery behavior to
   `@dag-orchestrator/neutral-execution`. The analytics adapter and tested
   legacy adapter consume the same package and neutral vectors. No analytics
   task calls the transitional root runner directly.
2. **Adapter policy is immutable at the call boundary.** The analytics adapter
   constructs `NeutralRunOptionsV1` with retries `0`, `maxAttempts: 1`, review
   and repair rounds `0`, `fallbackChain: []`, and final review `off`. It
   rejects caller overrides that contradict these values. A future
   controller-classified infrastructure attempt must use a new execution ID
   and reserved budget; this milestone does not retry failed analytical work.
3. **Principal and caller boundaries.** `Principal` and `SourceGrant` use the
   frozen fields in `docs/build/interfaces.md`. Missing identity, project
   mismatch, expired grant, wrong source, wrong operation, or wrong audience
   denies before protected callbacks run. `principals.ts` provides the
   production gateways consumed by the controller/context, source adapter,
   catalog, and viewer; `context.ts` calls the controller gateway directly,
   performs its synthetic source metadata read and catalog reference lookup
   through the source/catalog gateways, and reopens the resulting demo
   artifact through the viewer gateway. The acceptance fixture invokes all
   four caller surfaces, including both viewer metadata and byte reads, rather
   than testing only a grant predicate.
4. **Worker launch and search boundary.** The default analytics launch path
   compiles an allowlisted packet and a staged worker workspace from analytics
   instructions, the proposal contract, the question, and authorized
   references. `startAnalyticsWorkerV1` in `context.ts` is the default
   analytics worker launch entrypoint consumed by the forthcoming 003 CLI;
   the 001 live acceptance calls that entrypoint, not the context compiler in
   isolation. It does not discover prompts or examples by scanning the
   controller checkout. The launch record includes the exact context digest,
   file manifest, principal, grant IDs, harness/model/version, and execution
   reference. If the host does not confine shell reads to the staged workspace,
   label it workflow-level. `docs/build`, `.dag-build`, and build scripts stay
   in the controller checkout and are not copied into the analytics worker
   workspace.
5. **Inspectability before the full viewer.** The 001 demonstration writes a
   sanitized local Markdown record at
   `artifacts/neutral-execution/acceptance/001-live-demo.md` and prints its
   path. It shows question, supplied context digest/file list, enforcement
   label, neutral execution status, attempt count, and log/output references.
   It explicitly says that process success does not establish support. The
   purpose-built browser answer/trace page remains 011's responsibility.
6. **Python wire contracts.** Pydantic models are normative. `PlanRegistrationV1`
   is a discriminated union of `ExploratoryPlanV1`, `DescriptivePlanV1`,
   `PredictivePlanV1`, and `InferentialPlanV1`. Common envelopes and each
   computation mode are strict, extra-forbidding, and reject non-finite
   numbers. The dispatcher validates mode payloads and returns explicit
   unsupported-mode errors until a calculator owner exists; it never returns
   dummy data. `schema_export.py` generates checked JSON schemas from these
   models. Python validates shared positive/negative vectors with Pydantic;
   `contract-vectors.test.ts` validates the same vectors against those
   generated schemas with Ajv 2020-12. No handwritten TypeScript schema is
   maintained.
7. **Dependency floor.** Lock the shared numerical runtime dependencies
   needed downstream (`numpy`, `pandas`, `scipy`, `scikit-learn`) with Pydantic
   in `python/pyproject.toml` and `python/uv.lock`. Features 007/013/006 may
   consume but not revise the common lock.
8. **Factory removal without build-tool removal.** Remove inherited product
   modules, factory CLI entrypoints, prompts, examples, fixtures, task
   templates, skill installer, and factory viewer panels/default routes.
   Rewrite the root README for the analytics product and remove its dead
   installer instructions. Preserve the generic neutral execution
   compatibility surface and every build-orchestrator file. The analytics
   worker allowlist excludes build tooling and migration materials. The
   checked inventory at
   `artifacts/neutral-execution/acceptance/001-transitional-behavior-inventory.md`
   names each inherited behavior active in the fixture and separates it from
   build-only behavior, removed factory behavior, and analytics capabilities
   that are not implemented. Acceptance tests check that inventory and README;
   they do not report later analytics workflows as live.

## Project Structure

### Planning artifacts

```text
specs/001-isolate-analytics-product/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   ├── access-and-caller-gates.md
│   ├── neutral-execution.md
│   └── python-wire-contracts.md
├── quickstart.md
├── tasks.md
└── execution.json
```

### Implementation ownership

```text
packages/neutral-execution/             # sole neutral execution source
artifacts/neutral-execution/             # local archive, digest and demo record
python/pyproject.toml
python/uv.lock
python/analytics_worker/{__init__.py,__main__.py,contracts.py}
python/analytics_worker/schema_export.py
python/tests/contract-vectors/           # shared Python/TypeScript vectors
src/analytics/
├── context.ts                           # context compiler + default launch
├── principals.ts                        # shared grant gates/caller wrappers
├── runner-adapter.ts                    # analytics-only neutral options
├── schemas/                             # generated Pydantic JSON schemas
├── contract-vectors.test.ts             # TypeScript validation of shared vectors
└── acceptance/001.test.ts                # context, access and runtime evidence
pnpm-workspace.yaml
artifacts/neutral-execution/acceptance/
└── 001-transitional-behavior-inventory.md
```

The task list also removes the inherited product surfaces by their existing
paths. It leaves `scripts/build/`, `.dag-build/`, `docs/build/`, the taskflow
manifest, accepted specifications/checklists, and neutral compatibility
inputs untouched.

## Integration and Verification

The production chain is `configured principal → startAnalyticsWorkerV1 →
authorized context compiler →
authorized source metadata + catalog lookup → default analytics worker launch
→ analytics runner adapter → neutral package → viewer-authorized inspection
of the local execution record`. The same principal gate is consumed at all
four request boundaries. The test injects a generic
runner success paired with a failed analytical check and a valid negative
finding; it asserts no support inference and no automatic second attempt.

The 001 acceptance also validates all fixture-active transitional behavior
against the written inventory, confirms that build-runner policy stays outside
analytics runtime behavior, and checks the README's live-versus-planned
boundary. The T53 search covers both repository discovery paths and the
compiled worker context.

Neutral conformance compares dependency order, argv delivery, exit status,
stdout/stderr attempt logs, timeout, stop/cancellation, locking, working and
artifact paths, interrupted/crash recovery, and replay across the extracted
package and the frozen legacy adapter. The local archive is built from the
workspace package and its digest is computed from the produced bytes.

Plan and computation vectors cover each positive profile/mode plus unknown
fields, missing required fields, invalid discriminators, wrong mode payloads,
bad references, invalid ranges, non-finite values, and unsupported modes.
`schema_export.py` writes deterministic JSON schemas from Pydantic. Python
validates each vector with Pydantic; the TypeScript conformance test validates
the same vectors against the checked generated schemas. A schema drift check
ensures the checked snapshots still match their Pydantic source.

## Constitution Check (post-design)

The design preserves the accepted spec and all fixed thresholds. The staged
worker directory is an allowlisted workflow boundary, not a claim that a
local shell cannot read its parent checkout. The local demo presents neutral
process facts and an analytics-check fixture separately. No future answer
library, statistical calculator, evidence store, or authorization behavior is
described as live. Build-runner review/retry behavior remains a build
mechanism; it is not imported into analytics runtime options.

## Risks and Boundaries

- Legacy parity is established only for the named neutral regression vectors
  and frozen legacy adapter, not for every upstream CLI or factory workflow.
- Principal gates protect product-owned surfaces. They do not make raw local
  files secret from a worker shell that has broader OS permissions.
- The 001 inspection artifact is a local boundary demonstration, not the
  complete answer/evidence viewer or a supported analytical result.
- The Python lock predeclares compatible numerical dependencies; it does not
  implement descriptive, predictive, or inferential calculations.
- Factory build tooling is deliberately retained for product construction,
  isolated from the worker's normal workspace and context. This is not a
  claim that the controller repository contains no build instructions.

## CLI registry handoff

Feature 001 solely owns `src/commands/index.ts`. Its factory cleanup removes the inherited factory route and registers the fixed `analytics` command through a computed lazy import trampoline. The trampoline reports an explicit handler-unavailable error until feature 003 supplies `analyticsCmd(argv, cmd)` in its own `src/analytics/cli.ts`; other import and handler failures propagate. Feature 003 tests the production dispatcher path without editing the registry.
