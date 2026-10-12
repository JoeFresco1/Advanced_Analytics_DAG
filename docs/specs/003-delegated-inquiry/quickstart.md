# Quickstart: Delegated Inquiry Acceptance

This guide covers local synthetic fixtures and a separate live-harness demonstration. It never uses real customer data or an external service. Fixture answers and fault descriptions remain in the evaluator path and are not supplied to the worker.

## Prerequisites

- Node.js 20 or later and the repository's installed pnpm workspace.
- The build prerequisites from features 001, 002, 007, 008, 009, 010, 011, 012, and 013.
- The fixed synthetic fixture registry and its evaluator adapter.
- A local project principal with the source and viewer grants declared by the fixture profile.

## Deterministic acceptance

Build and run the project acceptance suite:

~~~powershell
pnpm build
pnpm test
~~~

The feature 003 test exercises:

1. Signal, null, and exact-delta boundary predictive answers against a fixed baseline/candidate and interval rule.
2. Denominator multiplication/shift, future-feature, immature-label, train/evaluation preparation leakage, stale successful checks, and swapped report-value faults.
3. Exploration followed by an outcome-informed revision, a planned search step, and a replacement worker inheriting earlier exposure and remaining allowance.
4. A valid negative comparison, a budget stop with reserved checks/reporting, and all six canonical inquiry outcomes independently of process exit code.
5. Extension registration, explicit missing optional providers, duplicate commands, missing dependencies, cycles, provider import/export errors, and required hook failure.
6. Interrupted terminal-intent/project-memory/completion-acknowledgement writes replayed with the same IDs; no completed result before durable acknowledgements.
7. Exact evidence trace from each supported displayed value, a zero count of opaque worker-only values as supported report values, and first-screen separation of prediction, causation, and non-improvement.

Each fixture record includes its source version, transform, Plan, program digest, environment, Execution, Result, checks, and evaluator-only expected behavior. A locally readable evaluation partition is labeled workflow-withheld; access isolation not enforced.

## Production CLI walkthrough

Use a registered synthetic fixture name from the local acceptance fixture registry:

~~~powershell
dag analytics ask --project <local-project-path> --question "Does a decline in recent usage improve churn prediction beyond tenure and plan?" --source <synthetic-churn-fixture> --model gpt-6-luna --reasoning xhigh
dag analytics answer show --inquiry <returned-inquiry-id> --open
dag analytics trace --claim <claim-id-from-answer>
~~~

For the null variant, submit the same question with the registered synthetic null fixture. The response should retain the measured estimate and uncertainty, use the frozen predictive classification, and state that non-improvement does not prove no relationship. It should not repeat analysis in search of a favorable result.

The browser first screen shows the scoped answer, population/period, estimate, interval, delta, limitations, and inquiry outcome. The inspector follows each supported assertion through Claim, Result, checks/policy, Execution, transformation, and source version. A stop or blocked integrity condition stays visible as an honest partial answer.

## Live harness demonstration

Run the same ask path with gpt-6-luna and xhigh reasoning on the approved local synthetic signal and null fixtures. Record the harness/model version, context/source digests, budget, invocation count, and produced evidence IDs. The demonstration is not a substitute for the deterministic fault suite and does not change frozen thresholds or references.
