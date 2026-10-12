# Bounded diagnosis before stronger implementation workers

User authorization: after three failures, a fresh Luna xhigh worker investigates
the cause, determines whether an allowed workaround exists, and proceeds when
the controller permits it. This amendment changes build recovery only, not
analytical rerun semantics or product evidence requirements.

## Trigger and budget

The implementation wrapper invokes a separate Codex execution after three
distinct rejected implementation attempts, then at boundaries six and nine.
Duplicate review events do not count twice. Infrastructure retries do not
consume the semantic rejection budget. The existing total implementation
budget and no-Astra policy remain unchanged. One diagnostic invocation per
boundary and accepted contract/unit revision is persisted before launch;
failed or interrupted diagnostics hold rather than relaunch indefinitely.
Each invocation is capped at five minutes and uses Luna xhigh, read-only
sandboxing, structured output, and no nested model launches.

## Allowed recovery

The controller may restore an altered frozen file only when its HEAD contents
match the existing accepted hash exactly. It validates all candidates before
writing any. It does not regenerate hashes, adopt new requirements, broaden
ownership, or restore an arbitrary older checkpoint over current product work.

The diagnostic recommendation is advisory. For an implementation defect with
a concrete repair inside the assigned ownership, the next attempt uses the
tier that reached the boundary, with the diagnostic evidence in its prompt.
This attempt still consumes the existing total budget. If rejected, ordinary
routing resumes; history is never reset. All unchanged deterministic and
independent acceptance checks remain required.

An explicit implementation-difficulty diagnosis may permit the existing model
ladder to proceed. Contract/ownership, environment, resource, or unresolved
causes hold for controller resolution. No model can grant authority to change
scope, checks, thresholds, frozen contracts, permissions, or resource policy.
An ownership change requires a coordinator amendment; there is no automatic
permission expansion based on a diagnostic model's text.

## Evidence and limits

Records, raw diagnostic events, schema, response, cause, references, action,
and verification recommendations live under
`.dag-build/diagnostics/<runId>/<taskId>/`. Controller baseline restores and
actual post-diagnostic routing are recorded in model-routing.jsonl.
These are incident records; they are not automatically activated global skills.

This is installed in the analytics build controller. It is not a built-in
feature of all lightweight DAG runs or Codex sessions. Diagnosis quality is
still model-dependent; schema validation does not prove a causal explanation.
Read-only investigation can inspect source and existing evidence, but tests
requiring writes belong to the subsequent sandboxed implementation and
unchanged acceptance gates. Exhausted implementation budgets are not reset.

Verification: deterministic tests cover trigger boundaries, idempotence,
malformed and unauthorized recommendations, provider errors, bounded routing,
contract-version binding, and exact-baseline restoration with negative controls.
