# Quickstart: Verify a Bounded Inquiry

This walkthrough uses the synthetic/local feature 012 fixtures. It does not require a real source, external service, paid model or live data.

## Prerequisites

- Node.js 20 or later and the repository's installed pnpm dependencies.
- Features 008, 009 and 010 and the neutral execution adapter available as graph prerequisites.
- No active locked run file is modified by these tests.

## Run the acceptance path

1. From the repository root, run pnpm build.
2. Run pnpm test; this rebuilds and executes the Node test suite, including src/analytics/acceptance/012.test.ts.
3. Inspect the feature 012 acceptance output and specs/012-bound-worker-inquiry/verification.md after its implementation unit has recorded actual evidence.

## Expected scenarios

- A valid exploration then directed comparison persists its proposal/admission record before the dispatch spy launches.
- A failed admission-event commit launches no task.
- Exact repeated identity without a new purpose is rejected; a known changed denominator is not exact; an unknown input version stays unresolved and does not by itself reject the proposal.
- Nested CPU/worker/source-query demand is summed once against remaining shared reservations. A hard-cap-required proposal is refused when the adapter cannot enforce the cap.
- Required-check and final-reporting reserves remain unavailable to optional branches. Exhaustion renders committed facts and the stop reason without another worker call.
- Six frozen inquiry stop fixtures resolve under canonical precedence even when only process exit status changes.
- An unfavorable valid analytical outcome causes no process retry. Explicit classified infrastructure retries preserve the failed execution and exact analytical inputs.
- A host with no active cancellation reports a scheduling cutoff; a host that passes the cancellation test can report the tested active cancellation capability.
- Missing token or monetary telemetry is shown as estimated or unavailable, never as an exact zero cost.
- Replacement harness context contains prior plan, result, exposure, policy and remaining allowance.

## Limits of the fixture

The local fixture proves only the adapter guarantees it actually exercises. Local reservations do not prove OS-level CPU, memory or source-byte enforcement. Monetary/token caps remain unavailable or estimates unless the adapter supplies a real observable and enforceable mechanism.
