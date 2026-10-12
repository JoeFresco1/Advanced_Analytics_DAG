# Research: Isolate the Analytics Product

This is bounded repository and contract research. No external research agents
were used. The frozen architecture and interface contract resolve the stack
and ownership decisions; there are no open clarifications.

## Decision: one workspace package owns neutral execution

**Decision:** Extract the reusable dependency scheduler, argv process launch,
logs, timeouts, locks, artifacts, stop, and recovery into
`packages/neutral-execution/`. Export the versioned neutral interfaces named
in `docs/build/interfaces.md`. Consume the package from the analytics adapter
and a compatibility adapter tested against the frozen legacy implementation.

**Rationale:** The architecture names this package as the sole scheduler and
neutral execution authority. Preserving behavior under one source avoids a
second edited runner while keeping analytics policy out of shared process
code.

**Alternatives considered:** Keeping `src/runner.ts` as the analytics runtime
source would leave a factory-oriented copy in product context and provide no
shared maintenance boundary. A second analytics scheduler would violate the
single-runner contract. Remote publishing is unnecessary because the build
requires a local archive and digest only.

## Decision: treat all access as a principal-bound request

**Decision:** Resolve principals from configured local session/CLI identity;
resolve roles and grants from project configuration. A request must carry the
resolved principal into the controller/context, source, catalog, and viewer
gate. Missing identity or grant denies before a protected callback runs.

**Rationale:** This follows the normative access contract and frozen
`Principal`/`SourceGrant` fields. Rechecking at the viewer prevents an earlier
worker grant from implicitly authorizing a later reader.

**Alternatives considered:** Treating a grant helper as sufficient would not
protect caller operations. Allowing worker-authored roles/grants would let a
worker enlarge its own access. Shell/file isolation cannot be asserted from a
TypeScript gate; report a workflow boundary when the host shell can escape.

## Decision: launch from an allowlisted worker workspace

**Decision:** Compile a context packet from analytics-owned instructions and
currently authorized references, then stage only those files and synthetic
local inputs in the worker's normal checkout/search root. The launch record
binds the context digest, manifest, principal/grants, question, harness/model,
and execution ID. Do not scan or copy generic repository instructions,
factory material, build-runner prompts, evaluator keys, or unapproved source
references.

**Rationale:** A context filter alone cannot meet the clean worker
search-path criterion. The staged workspace makes normal discovery paths
testable while the access label remains honest about any shell escape.

**Alternatives considered:** Filtering only the prompt leaves broad repository
search able to find migration materials. Claiming a filesystem sandbox without
host enforcement would violate FR-008 and I-13. Copying the entire checkout
would expose build tools and transitional instructions to normal discovery.

## Decision: neutral status carries no analytical judgment

**Decision:** The adapter supplies exactly one process attempt with zero
generic retry/review/repair counts, no fallback candidates, and final review
off. The acceptance fixture separately records runner exit and analytical
check outcome. Failed checks and valid negative values are durable evidence,
not scheduler rejection conditions.

**Rationale:** Proposal I-04 and the canonical check/claim vocabularies make
process success and analytical support distinct. An automatic favorable retry
would expose the worker to additional results and select on the outcome.

**Alternatives considered:** Prompt-only rules can be bypassed by the runner.
Reusing review rejection or repair routing would make software-review state
decide analysis. Treating a negative result as infrastructure failure would
erase a valid outcome.

## Decision: Pydantic is the single wire-schema authority

**Decision:** Use strict Pydantic v2 models for common envelopes, four Plan
registration profiles, and descriptive/predictive/inferential computation
payloads. Generate TypeScript-consumed JSON schemas and share conformance
vectors between Python and TypeScript. Reject extra/missing fields, invalid
discriminators, wrong-mode payloads, and non-finite numerics. The dispatcher
reports unsupported mode explicitly when a later calculator module is absent.

**Rationale:** This is the frozen interface rule and lets feature 008 validate
registration before calculator features land without introducing duplicate
schema definitions.

**Alternatives considered:** Hand-maintained TS/Python schemas can drift.
Arbitrary JSON payloads cannot reject mode mismatches at ingress. Placeholder
computations would fabricate results before their owning features exist.

## Decision: lock shared numerical dependencies in 001

**Decision:** Declare Pydantic, NumPy, pandas, SciPy, and scikit-learn in the
Python project and commit the uv lock now. Python 3.11+ is the supported
baseline. Later profile owners use the locked dependencies and do not edit the
manifest/lock.

**Rationale:** The predictive and inferential plans depend on commonly used
tabular, modeling, and interval primitives; one predeclared lock avoids
features independently changing the worker environment. No numeric
calculator is implemented in this milestone.

**Alternatives considered:** Deferring the lock to 013/006 would violate
single ownership and make computation binding depend on later environment
changes. Adding a general statistical framework is not required by the
accepted two-group profile.

## Decision: keep build orchestration outside product runtime cleanup

**Decision:** Remove factory/product-facing modules, prompts, command
entrypoints, fixtures, task templates, and default viewer semantics while
preserving `scripts/build/`, `.dag-build/`, `docs/build/`, locked build/run
manifests, accepted specs, and all feature checklists. Keep build reviews and
retries in the build runner only.

**Rationale:** The user explicitly requires build tooling to remain intact;
the product contract distinguishes implementation retry/review from
analytical attempt policy. The analytics worker workspace allowlist excludes
that tooling.

**Alternatives considered:** Deleting the build flow would block the approved
product build. Loading build instructions into the analytical worker would
reintroduce the wrong task framing. Editing accepted feature criteria or
thresholds is prohibited.

## Decision: local inspection artifact is the 001 viewer path

**Decision:** Write a readable local Markdown record with the real question,
context file list/digest, principal/access profile, neutral execution result,
attempt count, and safe log/output references; print its path. The full browser
answer/trace viewer remains feature 011-owned.

**Rationale:** FR-009 requires an inspectable effect in 001 while
`viewer/analytics.html` is explicitly owned by 011. The artifact gives a real
local inspection path without claiming that the complete answer viewer
already exists.

**Alternatives considered:** Repository edits or unit assertions alone do not
satisfy SC-009. Reusing the old factory run page would expose its default
software-review semantics.
