# 18-bootstrap research decisions

This is a continuation of the recovered 18-bootstrap plan. The research
resolves implementation choices from the accepted feature 018 specification,
the proposal, the 001 access/context boundary, and the frozen build interfaces.
It does not change those contracts.

## Decisions

| Topic | Decision | Rationale |
| --- | --- | --- |
| Fixture identity | Derive IDs from profile/version, generator version, seed, and stable case key. Hash canonical UTF-8 JSON with stable key ordering; exclude generation time. | Same inputs reproduce IDs and digests, while new generators or content produce new identities. |
| Worker and evaluator views | Use separate worker fixture and evaluator reference types and loaders. The worker receives only question, permitted synthetic source/version refs, grants, policy, and allowed task context. The evaluator gets labels, expected limits, and fault definitions after the response. | Matches proposal T01–T61, feature 018 FR-002/FR-014, and architecture's separate post-response interface. |
| Earliest context check | Run canaries at the node's 001 dependency floor through `buildAuthorizedWorkerContext` / the actual 001 worker packet serialization and digest path. Supply synthetic values shaped like later retrieved-card, source-skill, and cached-summary additions; do not depend on those later producers. | Prevents answer-key leakage from being deferred until feature 018 while keeping graph dependencies unchanged. |
| Host isolation claim | Label local checkout access `workflow-withheld; access isolation not enforced`. Test packet contents and digests, but do not describe the checkout as physically inaccessible. | The worker shell may be able to read local files under the accepted 001 and architecture contracts. |
| Source and grant profiles | Freeze synthetic source IDs/versions and grants for permitted aggregates, restricted rows/sheets, and denied, revoked, or expired access. Use the canonical Principal/SourceGrant contract and keep reference-key access out of grants. | Makes grant scope and denial behavior testable without real data or worker-authored authority. |
| Fixture contributions | Accept feature-scoped descriptors with an authorized feature owner, unique case IDs, fixture content digests, exact parent lock revision, independent expected outcomes/rubrics, and approval time. The registry supplies the admission timestamp from its trusted clock and validates approval and admission before the target batch start. Produce a new immutable child revision. | Enables future feature owners to contribute cases while preserving old evaluated batches and frozen expected outcomes. |
| Contribution file ownership | Contributors declare only their own exact case/descriptor files in their feature manifests. The 18-bootstrap manifest lists exact base files, not the whole fixture directory or contribution subtree. | Prevents the foundational node's manifest from blocking later feature-owned contribution files. |
| Batch selection | A batch pins a lock revision at start. Evaluator selection resolves from that exact revision, not the moving registry head. | Later additions cannot alter a running or completed batch. |
| ID collisions | Reject any reuse of an existing case ID with changed content or reference digest. Treat only a byte-identical replay of the same contribution binding as idempotent. | Stable IDs cannot silently acquire new semantics. |
| Threshold declaration | Register the ten initial feature 018 gates from their accepted literals. Keep project-owner approval actor/time pending until a real configured approval is supplied; do not invent either. Keep five no-match queries as a separate zero-false-direct-answer outcome. | Preserves the accepted threshold table, FR-011 approval requirement, and zero-tolerance no-match outcome. |
| Evaluation boundary | This node freezes inputs and exposes independent adjudication and evidence-registration APIs. Feature 018 owns whole-product scoring, release aggregation, and broad reliability statements. | Matches the build graph and avoids treating fixture/bootstrap acceptance as release evidence. |
| Proposal case ownership | Retain `proposalCaseRefs` as references to the accepted ownership register. Contribution support does not transfer T01–T61 primary acceptance ownership to 18-bootstrap. | The acceptance plan's one-to-one T01–T61 assignment remains authoritative. |

## Alternatives considered

- **One mutable lock file:** rejected because editing a lock after a batch
  starts would rewrite historical inputs. The initial JSON documents revision
  one; contributed cases create immutable child records.
- **One owner for the entire fixture directory:** rejected because the
  architecture explicitly allows feature-scoped contributions. The bootstrap
  execution manifest owns exact base files only.
- **Copy reference fields into worker fixtures and hide them by convention:**
  rejected because types/loaders must enforce separate views, and direct plus
  indirect context canaries must fail closed.
- **Use output observed during implementation to complete a missing key or
  label:** prohibited. Independent outcomes and rubrics must be locked before
  the target batch and remain unchanged after it starts.
- **Claim an enforced holdout because fixture files are omitted from a packet:**
  rejected because local shell access may bypass product surfaces.
- **Have 18-bootstrap score whole-product release readiness:** rejected because
  feature 018 owns full question scoring and release aggregation.

## Frozen inputs consulted

- `specs/018-evaluate-analytics-product/spec.md` and its checked quality
  checklist.
- `docs/specs/advanced-analytics-dag-proposal.md`, including every T01–T61
  case and invariant mapping.
- `.specify/memory/constitution.md`, canonical state vocabularies, and access
  and judgment contract.
- FR/SC and edge verification maps for feature 018.
- `docs/build/ARCHITECTURE.md`, `interfaces.md`, `acceptance-plan.md`,
  `graph.json`, and the feature 001 context/access contract.
- `docs/build/task-generation-contract.md` and the recovered 18-bootstrap
  artifacts.
