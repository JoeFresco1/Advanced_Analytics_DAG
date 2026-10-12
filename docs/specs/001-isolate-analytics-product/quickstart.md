# Quickstart: Verify the 001 Boundary

All examples use the synthetic/local fixture. Do not point the acceptance
worker at a live protected source. From a clean checkout, install the locked
workspaces and Python environment:

```powershell
pnpm install --frozen-lockfile
uv sync --project python --locked
```

## Verify the contracts and neutral package

```powershell
pnpm acceptance:001
pnpm --filter @dag-orchestrator/neutral-execution test
pnpm --filter @dag-orchestrator/neutral-execution build
uv run --project python pytest
pnpm build
pnpm test
```

The neutral conformance suite runs the same vectors against the extracted
package and the local legacy adapter. It covers dependency order, argument
delivery, exit status, logs, timeout, stop, locks, artifact paths, and
recovery/replay. Contract tests load shared Plan/computation vectors in both
languages and validate generated JSON schemas.

## Inspect the worker boundary

Run the 001 live acceptance with three neutral questions and the configured
`gpt-6-luna` / `xhigh` harness:

```powershell
pnpm acceptance:001:live
```

The acceptance starts the configured worker with a compiled analytics packet,
runs a controller-issued neutral command, and writes
`artifacts/neutral-execution/acceptance/001-live-demo.md`. Open that file to
inspect the question, context digest and permitted-file manifest, principal
and grant references, enforcement label, execution status, attempt count,
and safe log/output references. The demo states separately that process
success does not establish analytical support. A local shell without enforced
filesystem restrictions is labeled `workflow-level`.

The deterministic access fixtures also invoke the actual controller/context,
source adapter, catalog, and viewer gate callbacks with two synthetic
principals. Confirm that ungranted callbacks do not run and permitted
operations do. A viewer grant is rechecked independently from a worker grant.

## Final acceptance

```powershell
python scripts/audit_spec_coverage.py
pnpm acceptance:001
pnpm acceptance:001:live
```

The final run records the observed context and outcome in `verification.md`,
links each FR/SC and Edge Case to evidence, records the generated local
archive digest, and reports any unavailable host-isolation guarantee without
changing the accepted spec or checklist.
