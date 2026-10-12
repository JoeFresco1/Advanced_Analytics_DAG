# Source Discovery Contract

## Shared extension boundary

The provider follows the frozen AnalyticsExtensionV1 definition in docs/build/interfaces.md. source-discovery.ts exports analyticsExtensionV1 with schemaVersion 1, extensionId source-discovery, and dependsOn containing catalog. It does not edit the 003 loader or shared CLI/controller.

The provider contributes the frozen source discover command. Its input is a registered source ID, not a database path, SQL statement, grant, or command. The command resolves the authenticated principal and checks the current metadata grant before opening SQLite. Aggregate profiles and recipe tests additionally require the aggregate operation. Missing or expired grants deny reads.

The `orient(context)` hook is the production cold-source discovery entrypoint during ordinary `ask`. It resolves the source from the existing 003 source bindings and reads currently accessible active source-skill record refs from committed 009 project memory through 014's access-filtered projection over the committed 009 project head. It matches refs on the exact source identity, environment, and tenant. This is a presence check only: 014 does not assess compatibility, call a 003 or 015 compatibility service, or consume a compatibility signal. If one or more accessible active refs match, it returns safe refs only and does not rediscover; the later 015 orientation hook owns the sole compatibility judgment and may classify guidance as incompatible or unresolved. If zero active refs match, it checks current metadata and aggregate grants plus remaining discovery budget, then invokes the same bounded discovery implementation used by `source discover`. Stale or revoked records do not count as active, but 014 does not infer revoked authority or bypass current access checks. An explicit `source discover` call remains available for refresh. This is not a second adapter or profiling path.

On the cold path, the hook returns only the access-filtered candidate reference and scope metadata for authorized inspection. It labels the result `candidate`, commits no activation event, and does not supply candidate SKILL.md content as active instructions. The only project-memory write from cold orientation is the new immutable candidate record/package through 009; it creates no active-use, activation, or answer record. When active refs already exist, the hook returns safe refs only and creates no candidate. Active skill compatibility, reuse, and activation remain owned by later source-reuse/activation features.

The 014 acceptance fixtures exercise the production hook with zero and one-or-more currently accessible active refs. The zero-ref case proves exact-scope cold discovery uses the shared command implementation and requires no compatibility service; stale/revoked or sibling-scope refs do not count as active. The active-ref case proves 014 returns safe refs only, does not rediscover, and does not call or require a 015 compatibility service; the later 015 hook owns compatibility. Separate hook cases omit or expire each required grant and exhaust discovery budget; they assert no adapter invocation and no project-memory commit. The cold-hook case is distinct from, and independently invokes, the `source discover` command case.

## Discovery request and result

The internal request binds the source ID, principal and project role, applicable grant reference, environment and audience, selected profile scope, fixed bounds, and discovery execution ID. Worker or metadata text cannot override any field.

The result contains the source scope, schema metadata allowed for the audience, bounded aggregate summaries, source-version/snapshot facts, stop reason if capped, exact query/parameter and execution references, assertion records, recipe-test evidence, and the committed candidate reference. It omits raw rows, local connection paths, credentials, and uncontrolled source text.

The SQLite bridge uses the shared executeStageV1 path. It opens a read-only URI, enables query_only, begins a consistent read transaction for the discovery, admits only one SELECT/EXPLAIN statement per recipe, uses bound values, and interrupts work at its admitted limit. This establishes a read-only database connection for the adapter; it does not claim to isolate a broader local worker shell.

## Candidate package contract

The package follows the source-skill structure in proposal section 13:

- SKILL.md is a short entrypoint with source/environment/role/audience scope, safe access route, references, tested recipes, known limits, and pitfalls.
- references/schema.md, definitions.md, joins-and-grain.md, and time-and-freshness.md are scoped to the exact source.
- examples/query.py uses bound parameters and a read-only connection.
- manifest.json binds the immutable package digest, source scope, audience, provenance, tested source version/window, and candidate lifecycle state.
- checks/manifest.json records exact recipe executions and what they tested.

Claim provenance distinguishes declared, observed, tested_on_version, and inferred. A definition unsupported by an authoritative declaration retains the unknown sentinel. A conflict keeps the named authority and both evidence references. This feature never transitions a package out of candidate.

## Publication and access

The candidate package is serialized as immutable project-memory content and submitted through 009 commitProjectMemory with a stable commit ID and expected project-manifest revision. Same-ID changed-content reuse is rejected by 009. 014 reads only source-skill IDs and scope/availability refs reachable from the committed 009 project manifest, resolves those records, and applies 001 filterAccessible before active-state/exact-scope matching. Staging/orphans are invisible. The 019 catalog indexes artifacts under its own contract; this feature does not implement catalog storage or rebuild logic.

The package and all references use the source audience grant. A reader with a narrower role receives no table or column metadata from the privileged candidate. Sensitive metadata and restricted paths are withheld before command output, extension orientation, package rendering, or catalog search.

## Untrusted text and safe query rules

Schema comments, table/column labels, stored SQL, and source examples are untrusted data. Raw comments and examples are omitted from generated guidance by default. If later review needs to inspect a source-text object, retain only an audience-scoped evidence pointer/digest in the package; do not place raw text in SKILL.md, command arguments, or privileged context. Owner-authorized definitions enter separately as declared assertions with a named authority reference. Dynamic SQL is limited to a single read-only statement. Values are placeholders with a separate parameter object; schema identifiers must match the discovered metadata inventory and use SQLite identifier quoting. One statement, a row/sample ceiling, statement progress deadline, output-size ceiling, and the stage timeout apply together.

Study-specific analytical outcomes are excluded from source skill content. If an outcome-bearing input is encountered, it is not copied into SKILL.md or generic source references.
