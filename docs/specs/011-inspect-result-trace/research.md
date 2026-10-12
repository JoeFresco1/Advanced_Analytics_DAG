# Research: Numerical report bindings and browser evidence trace

This is a bounded design synthesis of the accepted feature specification and
the frozen shared contracts. No external research or new dependency was
needed. The checkout has no indexed CodeGraph database and no analytics
implementation yet; its existing package provides Node's built-in test
runner and a headless Edge/CDP viewer-test pattern.

## Decisions

### Read committed evidence through existing ownership boundaries

**Decision**: Resolve answers and traces from 009 committed manifest references
and immutable records. Consume 010 CheckResult and frozen policy revisions.
Do not create another report store or duplicate the check-support predicate.

**Rationale**: The manifest is the visibility boundary. A supported numerical
assertion requires exact Claim, Result selector, Check Results, execution,
Plan, program/environment, transformation, source/data version, and output
bindings. The viewer must render historical and current availability
separately.

**Alternatives considered**: Reading staging files or run paths directly was
rejected because staged/orphan files are not committed evidence and paths do
not establish identity.

### Derive each displayed number from its bound Result selector

**Decision**: Validate the assertion's exact selector, unrounded value, unit,
denominator, scope, and rounding rule against the committed result. Compute
the shown number from the selected value and declared rule; show the full
selector and unrounded value in the trace.

**Rationale**: A narrative can swap values or make a material difference
disappear through rounding. T11 and SC-010 require deterministic detection
before supported presentation.

**Alternatives considered**: Trusting a supplied formatted string was rejected
because it cannot prove that the shown value corresponds to the result.
Recomputing the analytical result in TypeScript was rejected because Python
is the authoritative calculator.

### Serve one static shell with separate answer, trace, and artifact APIs

**Decision**: Serve the same viewer/analytics.html shell from stable inquiry
and claim routes. The page selects a fixed answer or trace API route from its
validated route ID. Use separate artifact metadata and bytes requests.

**Rationale**: A static shell avoids inserting report text into server-built
HTML. The JSON API is mountable by the fixed analysis path before 003 exists
and can be used by the later answer show and trace commands.

**Alternatives considered**: Rendering arbitrary report text into the shell
was rejected because it creates an active-markup boundary. A new web
framework was rejected because the shared stack already uses Node's HTTP
server and the feature has no need for another package.

### Check grants on every metadata and byte request

**Decision**: Resolve the authenticated Principal and current grant on each
request. Require metadata access before returning trace details and the
appropriate source operation before artifact bytes. Filter fields and paths
for metadata-only readers.

**Rationale**: The viewer's authority is independent of earlier worker
access; revocation and redaction must take effect immediately. The principal
and SourceGrant contract defines absent, expired, revoked, or narrow grants
as denial.

**Alternatives considered**: Reusing the worker's grant or caching viewer
authorization across requests was rejected because it would bypass current
access scope.

### Resolve artifact IDs and canonicalize only declared roots

**Decision**: Accept stable artifact IDs only. Resolve the declared record
under the committed manifest, authorize the current request, canonicalize
both artifact and approved root, reject traversal/symlink escape, then serve
declared bytes with safe content handling.

**Rationale**: Report content is untrusted and must never choose a filesystem
path. Metadata-only readers must not receive restricted paths or raw rows.

**Alternatives considered**: Accepting a report-supplied path or serving
active HTML/SVG inline was rejected because either could grant arbitrary
filesystem access or execute content.

### Use the existing built-in test and browser tooling

**Decision**: Keep acceptance in the graph-owned
src/analytics/acceptance/011.test.ts. Use Node's test/HTTP/fetch APIs and the
existing headless Edge/CDP pattern for a real click from a report value to a
data-version trace. A missing browser is an acceptance failure, not a
successful skip.

**Rationale**: The feature explicitly requires a browser click path. This
uses existing local tooling and leaves package manifests and shared tooling
ownership unchanged.

**Alternatives considered**: A DOM-only string test or an HTTP-only test was
rejected because neither proves that a user can click a displayed value and
reach the trace. Adding Playwright or changing the build scripts was rejected
because package/tooling files have another owner.

## Resolved assumptions

- 009 provides committed-run reads and artifact identity/availability; 010
  provides frozen check outcomes and supported-presentation eligibility.
- Access checks are injected through the existing Principal/SourceGrant
  services owned by 001.
- The generic inquiry and claim shell responses reveal no report metadata.
  The JSON endpoints apply grants before returning metadata.
- Any local shell-readable source is labeled workflow-level. The viewer does
  not claim that a product-surface grant independently isolates the worker.
- Report completeness is computed from the required structured first-screen
  fields; worker prose cannot mark an incomplete report complete.

No unresolved technical clarification remains.
