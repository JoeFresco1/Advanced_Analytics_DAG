# HTTP Contract: Answer and evidence trace

## Server boundary

src/analytics/http.ts exports a versioned, mountable Node HTTP handler plus
stable inquiry/claim URL builders. The handler receives injected services for
the authenticated Principal, committed manifest/record reads, grant checks,
artifact roots, and safe artifact serving. It does not read worker staging
directories or trust paths embedded in reports.

The handlers use stable IDs as route parameters. IDs are decoded once and
must remain one path segment; encoded separators, traversal strings, malformed
IDs, and arbitrary path/query values are not interpreted as filesystem paths.

## Routes

| Method and path | Response | Behavior |
| --- | --- | --- |
| GET /analytics/inquiries/{inquiryId} | text/html | Serve the same generic viewer/analytics.html shell. Do not embed inquiry data in the response. This is the route for answer show --open. |
| GET /analytics/claims/{claimId} | text/html | Serve the same generic viewer shell. Do not embed claim data in the response. This is the route for trace --claim. |
| GET /api/analytics/inquiries/{inquiryId}/answer | application/json | Resolve the committed inquiry/report and return its answer-first ReportViewV1 after current grants are applied. |
| GET /api/analytics/claims/{claimId}/trace | application/json | Resolve committed Claim, Result, checks and the exact ordered trace after current grants are applied. |
| GET /api/analytics/artifacts/{artifactId}?view=metadata | application/json | Resolve a committed artifact ID and return only the fields allowed by the current metadata/aggregate grant. Restricted paths and raw values are omitted. |
| GET /api/analytics/artifacts/{artifactId}?view=bytes | declared safe content | Require the artifact's current data operation, canonicalize beneath its approved root, reject traversal and symlink escape, and return only its declared bytes. Active content is attachment-only with nosniff. |

The inquiry and claim shell routes are intentionally separate public entry
points even though they return one static file. Both are tested directly and
through a browser. The answer and claim API routes are also tested as real
HTTP requests against committed synthetic records. These contracts let the
002 fixed-analysis integration mount the handler before 003 exists and let
003's answer show and trace commands use the same URL builders later.

## Response behavior

- Successful shell responses set text/html with UTF-8, a restrictive
  Content-Security-Policy, and X-Content-Type-Options: nosniff.
- Successful JSON responses contain only authorized presentation metadata and
  stable evidence refs. They do not return raw source rows or restricted paths
  to metadata-only readers.
- Answer and trace responses include the access-safe generic linked-provenance
  projection reachable from the committed inquiry/assertion. Resolve each
  typed RecordRefV1 by exact record ID, revision, and digest through committed
  manifests, then apply the current grant for its source scope before returning
  metadata. Keep missing, changed, unavailable, or restricted links visible
  with bounded safe labels; never return raw source text, protected titles or
  paths, protected values, or uncommitted record content.
- A source or record that is absent, changed, stale, unavailable, redacted,
  restricted, or denied stays visible in the answer/trace with a safe reason.
  The handler does not turn it into a supported value.
- Denial and not-found responses do not disclose whether an inaccessible
  protected object exists. Error bodies use bounded reason codes and omit
  source values, raw paths, and stack traces.
- Artifact byte responses use a safe content type, Content-Disposition
  attachment for active content, and X-Content-Type-Options: nosniff.
- The page obtains all text through the API and inserts it only as text nodes.
  It constructs request URLs from the known route templates and encoded IDs.

## Required bindings

An answer response includes question, answer, population, period, material
assumptions, uncertainty, limitations, interpretation, assertions, charts,
first-screen completeness, current eligibility, and source-boundary wording.
Each assertion includes the exact Result selector, unrounded value, unit,
denominator, rounding rule, scope, uncertainty, Claim/check/policy refs, and
links to Plan, computation, transformations, source/data version, and outputs.
Each chart includes source aggregation, selector, axes, scale, filters, labels,
and uncertainty meaning. Report wording cannot replace any binding.

### Generic linked provenance projection

The answer and claim-trace JSON include typed RecordRefV1 relationships for
`source_version`, `skill_version` (including canonical status when permitted),
`independent_validation`, `authority_decision`, `supplied_reference`,
`executed_recipe`, and `outcome_exposure` when linked records exist. These are
generic read relationships; 011 does not require later feature writers or
interpret raw record payloads. Preserve each exact ID/revision/digest and
show a bounded relationship label and access/link condition. Resolve only
committed records and check current access for each source scope. An absent,
changed, or restricted target remains visible as such without exposing raw
source text, protected titles/paths/values, or uncommitted content.

The HTTP acceptance fixture commits synthetic examples of all seven
relationship types and issues real requests to both answer and claim-trace
endpoints. The headless browser loads that answer and trace and asserts that
the generic links are visible. It also uses paired source-boundary fixtures:
the locally workflow-readable source displays `workflow-level`; the
independently permissioned source displays `independently permissioned
source`. Both paths assert zero ungranted raw values and restricted paths.
These cases do not depend on 014/022 and let 004 consume the existing viewer.

## Production reachability and browser acceptance

The exported handler must be mounted with Node's HTTP server in the acceptance
test using synthetic committed records. The test requests both shell routes,
loads an inquiry page in headless Edge, clicks a displayed assertion, observes
the claim-trace API request, and reaches the committed data-version entry.
The browser test also verifies that malicious source text executes no action,
that every chart's trace shows its bound aggregation, axes, labels, uncertainty,
and source Result, and that all seven linked-provenance relationship types are
available with safe status. It asserts both SC-008 boundary labels on the
paired fixtures and verifies neither response includes ungranted raw values.
Browser startup failure is a failed acceptance condition, not a skipped pass.
