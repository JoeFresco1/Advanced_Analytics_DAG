# Implementation Plan: Numerical report bindings and browser evidence trace

**Branch**: 011-inspect-result-trace  
**Date**: 2026-09-28  
**Spec**: [spec.md](spec.md)

## Summary

Deliver an answer-first browser report backed only by committed records. Each
number and chart is validated against an exact Result selector and remains
clickable through its Claim, current Check Results, Execution, Plan,
transformations, and source/data version. A current grant is checked before
evidence metadata or artifact bytes are returned. Missing, stale, changed,
redacted, unavailable, or denied evidence remains visible with an access-safe
reason. The first screen is incomplete if answer, population, period, material
assumptions, uncertainty, or remaining limitations is absent.

Implement the report resolver in src/analytics/report.ts, a mountable Node
HTTP handler and stable route builders in src/analytics/http.ts, the
answer/trace page in viewer/analytics.html, and the focused acceptance suite
in src/analytics/acceptance/011.test.ts. The fixed analysis producer in 002
and the answer/trace CLI owned by 003 consume these routes; this feature does
not edit their shared files.

The same answer and trace projection also exposes generic committed linked
provenance reachable from the inquiry and assertion: source version, skill
version/status, independent validation, authority decision, supplied
references, executed recipes, and prior-outcome exposure when present. Each
link remains a typed RecordRefV1 with its exact record ID, revision, and digest.
Resolve each link from committed manifests and apply the current reader grant
before returning its metadata. Missing, changed, or restricted links stay
visible with bounded safe labels; never include raw source text, protected
titles or paths, or uncommitted records. Verify the projection using
synthetic committed linked records in 011-owned report, HTTP, viewer, and
acceptance files, without depending on unbuilt 014/022 behavior.

## Technical Context

**Language/Version**: TypeScript 5.6, Node.js 20 or newer, browser JavaScript  
**Primary Dependencies**: Existing analytics record, grant, artifact, and
check contracts from 001, 009, and 010; Node built-in HTTP, test, fetch, and
WebSocket APIs. No new package dependency.  
**Storage**: Committed run/project manifests and their immutable records and
declared artifact roots. 011 adds no independent store or persistence stream.  
**Testing**: Node built-in test runner with synthetic committed-record
fixtures, HTTP integration, and headless Edge/CDP browser acceptance in
src/analytics/acceptance/011.test.ts. Browser coverage must fail if the
required browser path cannot run; it must not silently count a skipped browser
case as acceptance.  
**Target Platform**: Local Node service opened in a user browser.  
**Project Type**: TypeScript server facade plus static browser page.  
**Performance Goals**: Resolve one answer or trace from a bounded set of
committed references per request; stream only a declared artifact after
authorization. The accepted spec defines no latency target.  
**Constraints**: Stable record/artifact IDs are the only lookup authority.
Never use a worker-authored path to select a file. Apply current grants before
returning evidence metadata or bytes. Keep the exact committed revisions and
digests attached. Preserve negative, null, inconclusive, failed, conflicting,
and incomplete evidence. Do not calculate analytical results in TypeScript.
Use synthetic/local fixtures only. Do not change package manifests,
shared architecture, controller/CLI ownership, frozen policy, or accepted
specifications.

## Constitution Check

| Gate | Result | Design evidence |
| --- | --- | --- |
| Honest answer and visible scope | Pass | The first screen is built from explicit answer, population, period, assumptions, uncertainty, and limitations fields; missing fields make it incomplete. |
| Execution and evidence remain separate | Pass | A report assertion references an exact Result selector and Claim; eligibility is derived from current bound checks rather than process status or a generic verified badge. |
| History and revisions remain attributable | Pass | Every trace item retains its EvidenceRef revision and digest. Changed and missing links stay visible without rewriting historical records. |
| Accurate access guarantees | Pass | The current Principal and SourceGrant are checked for each API request. A locally readable worker source is labeled workflow-level; the viewer makes no filesystem-isolation claim. |
| End-to-end consumer and seeded failure | Pass | A synthetic committed producer fixture reaches the real handler and browser. Tests click a report number through to its data version and inject numerical swaps, missing evidence, denial, unsafe markup, and unsafe paths. |
| No software-factory domain semantics | Pass | The feature implements report and evidence inspection only; it does not add repair, retry, or review-round outcomes. |

No constitution exception is required.

## Design

### Production path

The 009 committed-manifest reader is the sole source of visible records.
Feature 010's frozen policy and CheckResult records determine current
supported-presentation eligibility. The report resolver verifies assertion
selectors and chart source bindings against those records and returns a
presentation DTO. The HTTP handler applies a current access check on each
answer, trace, metadata, and bytes request. The browser page renders the
answer first and fetches the selected trace only after a value or chart is
opened.

The resolver also projects generic provenance references already linked from
the committed inquiry/assertion. It recognizes source-version,
skill-version/status, independent-validation, authority-decision,
supplied-reference, executed-recipe, and outcome-exposure relationships as
typed RecordRefV1 links; no relation requires a feature-specific reader or
writer in 011. It resolves the exact ID/revision/digest through committed
records, checks the current principal's permission for each disclosed scope,
and emits bounded labels and link status. Missing or changed refs and denied
links remain visible without protected content. Synthetic linked records
exercise both real JSON APIs and the browser projection now, so later 004
records use this viewer path without a viewer edit.

The HTTP surface exports stable builders for the answer and trace URLs, a
generic inquiry shell route, a generic claim shell route, and separate JSON
routes for answer, claim trace, and committed artifacts. The 003 CLI can use
the same builders for answer show and trace. The 002 fixed-analysis path can
mount this handler before 003 exists. The acceptance test starts the exported
handler against synthetic committed records and proves that the page, APIs,
and value-to-source-version interaction are reachable together.

### Binding and presentation

An assertion identifies the exact Claim EvidenceRef, Result EvidenceRef,
selector, unrounded value, unit, denominator, rounding rule, population and
period scope, uncertainty definition, and relevant check/policy references.
The resolver compares those fields to the selected committed result. It
derives the displayed number from the selected result and declared rounding
rule; report prose cannot override it. A mismatch blocks supported display
for that assertion and stays visible with the actual value and safe reason.

T11 fixtures independently swap metric value, unit, and denominator and
assert each mismatch is caught before supported display. A committed ref with
the expected ID but a different revision or digest is likewise visible as a
changed binding.

A chart identifies its source Result selector and aggregation, axes, scale,
filters, labels, and uncertainty meaning. The trace shows these bound plotting
choices so a changed scale or filter is inspectable. Narrative fields preserve
the Claim's stated prediction, association, or causal interpretation and its
negative or inconclusive evidence state. Browser acceptance opens every chart
in the fixture and asserts its aggregation, axes, scale, filters, labels,
uncertainty definition/encoding, and exact source Result ref; a planted
aggregation, scale, or filter change remains visible as a binding mismatch.

### Trust and access boundary

The handler resolves IDs through committed manifests and obtains the current
Principal and source scope from injected analytics services. It checks
metadata or data operations before returning corresponding metadata or
bytes. It never accepts a filesystem path from report content. Artifact
bytes are resolved beneath the approved root after canonicalizing both root
and artifact path; traversal and symlink escapes are rejected. Active content
is not rendered inline. Errors omit protected values and filesystem paths.

The static page uses DOM text nodes/textContent for report and source text,
constructs links only from encoded stable IDs, and never inserts untrusted
content as markup. Security headers restrict active content. The report keeps
access-safe link states for missing, changed, stale, unavailable, redacted,
restricted, or denied evidence without labeling any such state as supported.

Boundary acceptance uses a paired fixture: a workflow-readable source whose
bytes remain locally readable to the worker, and an independently permissioned
source whose separate grant boundary denies ungranted bytes. The browser shows
the exact SC-008 labels (`workflow-level` and
`independently permissioned source`) for their respective fixtures and
asserts that neither viewer path returns ungranted raw values or restricted
paths. These labels describe the fixture's actual boundary; they do not
upgrade workflow controls into filesystem isolation.

## Project Structure

### Documentation

~~~text
specs/011-inspect-result-trace/
├── plan.md
├── research.md
├── data-model.md
├── contracts/
│   └── http.md
├── quickstart.md
├── tasks.md
├── execution.json
└── verification.md   # Written by the final implementation/acceptance unit
~~~

### Source code

~~~text
src/analytics/
├── report.ts
├── http.ts
└── acceptance/
    └── 011.test.ts

viewer/
└── analytics.html
~~~

**Structure Decision**: Keep all analytics behavior under src/analytics and
the user page under viewer, matching the frozen graph. The report resolver
owns assertion/chart binding; the HTTP facade owns routes, access checks, and
artifact serving; the page owns answer-first rendering and trace interaction.
The acceptance test exercises the production handler and browser path.

## Risks and decisions

- A viewer grant can differ from the worker's earlier grant. Resolve the
  current Principal and grant on every request; do not reuse worker access.
- A result can remain historically committed while current artifacts are
  unavailable or redacted. Keep the historical EvidenceRef visible and state
  current availability separately.
- A syntactically valid report can omit a required first-screen field. Compute
  completeness from the named structured fields and explicitly test omission
  of each field.
- Browser-only coverage can disappear if it is optional. The feature
  acceptance test requires the headless browser and records browser failures
  as failures.
- Numerical presentation can diverge from narrative wording. Derive displayed
  numbers from Result selectors, preserve the unrounded value and rounding
  rule, and test planted value/unit/denominator/scope/rounding mismatches.

## Complexity Tracking

No constitution violations or extra architectural components are introduced.
