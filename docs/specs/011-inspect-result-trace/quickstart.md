# Quickstart: Inspect a committed answer and trace

This guide describes the feature acceptance path using synthetic/local
fixtures only. It does not invoke the analytics runner or access remote data.

## Prerequisites

- Complete the 009 committed-record and artifact interfaces and the 010
  frozen-check interfaces.
- Install the repository's existing Node dependencies.
- Use the repository-supported Node version and a headless Edge browser for
  the browser acceptance case.

## Run acceptance

~~~powershell
pnpm build
pnpm test
~~~

The 011 acceptance suite starts the exported analytics HTTP handler with
synthetic committed evidence, checks the inquiry and claim shell routes and
JSON APIs, then drives the browser. The browser opens the inquiry route, shows
the answer first, clicks a report number, follows the exact Result/check/Plan/
transformation/source trace, and opens the data-version metadata. It injects
changed and missing links, omitted first-screen fields, value/unit/denominator/
rounding/scope swaps, denied grants, active markup, traversal, and a symlink
escape. Expected outcomes are visible unsupported/incomplete/access-safe
states with no unauthorized bytes.

The committed fixture also links generic RecordRefV1 provenance for source
version, skill version/status, independent validation, authority decision,
supplied references, executed recipes, and outcome exposure. Both real JSON
endpoints and the browser verify those links without requiring future 014/022
records. Paired fixtures show the `workflow-level` and
`independently permissioned source` SC-008 labels and return zero ungranted raw
values or restricted paths. Browser checks open every chart and assert its
aggregation, axes, scale, filters, labels, uncertainty meaning, and source
Result.

The suite reports evidence for each SC-001 through SC-010, all three ordered
edge cases, and proposal T11/T60. It does not reinterpret these checks as
statistical proof or as source isolation beyond the fixture's actual
permission boundary.

## User route contract

- The answer entry point is GET /analytics/inquiries/{inquiryId}; its JSON
  data comes from GET /api/analytics/inquiries/{inquiryId}/answer.
- A claim entry point is GET /analytics/claims/{claimId}; its trace data comes
  from GET /api/analytics/claims/{claimId}/trace.
- Committed artifact metadata and bytes use stable artifact IDs under
  /api/analytics/artifacts/{artifactId}; a report-supplied path is never used.

The 003 answer show and trace commands consume the exported URL builders. The
002 fixed-analysis integration mounts the same handler over committed evidence
before the later inquiry controller is available.
