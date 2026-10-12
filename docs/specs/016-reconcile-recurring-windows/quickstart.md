# Quickstart: Recurrence Acceptance

This guide is for the implementation worker after prerequisites 003 and 015 are present. It uses only synthetic/local records. It does not start an analytics runner, access external data, or use evaluator reference answers as worker context.

## Prerequisites

- Node.js 20+ and the repository's installed pnpm dependencies.
- 003's fixed extension registry, answer/trace baseline, and controller services.
- 015's registered `source-reuse` provider.
- 009's run/project commit and recovery APIs.

## Feature fixture

Run the focused acceptance fixture:

```powershell
pnpm exec node --import tsx --test src/analytics/acceptance/016.test.ts
```

The fixture should demonstrate:

1. A new synthetic bounded window produces a distinct source/execution version; identity includes event and processing bounds, completeness, source position, lateness, and applicable label maturity.
2. Matching metadata is `comparable`; adjusted coverage with a predeclared adjustment is `qualified_comparison` with an interpretation limit; a changed definition is `incomparable`; missing required metadata is `unassessed`.
3. A provisional answer/trace shows completeness and lateness limits.
4. A provider registered through 003's fixed loader receives dependency-ordered orientation, inspection, evidence-commit correction, and finalization dispatch. Its output refs resolve through answer show and claim trace.
5. A synthetic multi-hop graph spans all nine dependent types. Two successive corrections identify the complete reachable set, preserve all prior refs, and flag each changed-bound record before a semantic judgment. The fixture includes retained and unavailable earlier data.
6. An undeclared repeated look remains exposure/outcome-informed and cannot be independently confirmatory; a declared sequential/multiplicity policy records and evaluates every look.

## Repository checks

Run the full type/build and repository test commands after the focused fixture passes:

```powershell
pnpm run build
pnpm test
```

`pnpm test` includes the build. A process exit is not analytical support; read `verification.md` for the per-FR/SC evidence, fixture limits, and command outcomes. No policy threshold or reference answer is modified by these checks.
