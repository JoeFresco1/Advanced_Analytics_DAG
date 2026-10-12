# Data Model: Recurrence and Consequential Use Integration

005 introduces no durable domain record type or state vocabulary. It composes existing owner records and exposes their exact references in the integrated answer/use view. Persisted records remain immutable; current status is projected from linked assessments and validity events.

## Existing records consumed

| Record | Owner | Fields/meaning used by 005 | Validation and relationship |
| --- | --- | --- | --- |
| Source/window version | 016, based on source identity from 007 | Stable source and version reference; event-time and processing-time bounds; completeness/finalization; source position; lateness; label maturity when relevant; definition and population versions. | A recurrence creates a distinct source and execution version. Missing required window identity leaves the comparison `unassessed`. Event-time correction and arrival history remain separate. |
| Correction relationship | 016 | New source-version reference, corrected prior version, correction reason, event/processing times, and impact references. | Append a new relationship for each correction. Two corrections retain both links and recompute impact from each source revision. |
| Period comparison | 016 | Exact compared window and definition refs, assessment rationale, and canonical state. | Uses only `unassessed`, `comparable`, `qualified_comparison`, or `incomparable`. A same-schema business-meaning change is still a definition mismatch. |
| Skill version and use record | 015 | Exact source identity, scope/role, definition/schema compatibility facts, skill version and use reference. | Lifecycle state remains `candidate`, `active`, `stale`, or `revoked`. 005 shows the version actually supplied and lets 015 decide compatibility/freshness; corrected meanings invalidate only affected guidance. |
| Plan and exposure record | 008 / 016 | Exact Plan revision, declared look schedule and procedure, observations exposed, actor/session, and exposure provenance. | Interpretation remains `planned_confirmatory`, `exploratory`, `outcome_informed`, or `exposure_unknown`. An undeclared additional look is exposed and cannot become an independent confirmatory label. |
| Result and Check Result | 009 / 010 | Stable ID/revision/digest; exact inputs and Plan; validator/policy binding; observed result/check outcome. | Changed bound inputs make prior checks insufficient/stale as their owner specifies. A failed or inconclusive result remains evidence. |
| Claim and review | 017, using 010 eligibility | Exact Claim revision, scope, supporting/challenging Result refs, evidence state, reviewer identity/type, rationale and unresolved issues. | Claim evidence stays `supported`, `unsupported`, `inconclusive`, or `conflicting`. Human review never changes an evidence state or exposure label. |
| Report assertion/artifact | 011 / 019 | Report assertion selector, unit, denominator, rounding, artifact reference and exact Claim/Result refs. | 011 serves committed IDs after current access checks. The report shows stale/missing/restricted links and permitted explanations; paths are not authority. |
| Use Assessment | 017 | Requested use/audience, exact Claim, Result, Check, Review and policy revisions; actor; rationale; unresolved issues; outcome. | Canonical authorization state only: `not_assessed`, `pending_review`, `authorized`, `risk_accepted`, `denied`, `stale`, or `revoked`. A material bound change stales the current assessment and a new linked assessment resolves it. |
| Decision | 017 | Selected action, actor, exact Claim revision(s), timestamp, constraints, external action reference, and linked Use Assessment. | A new decision references the assessment that permitted its action. The historic Decision remains inspectable after correction; its pointers are not rewritten. |
| Reproduction assessment | 009 | Declared exact-byte, canonical-table, or numerical-tolerance rule and observed comparison. | State remains `not_attempted`, `reproduced`, `mismatched`, or `unavailable`; availability is separate and no state is inferred from exit code or historical availability. |
| Answer-card reference | 020 | Stable record ID/revision/digest and permitted summary/validity metadata when present in the fixture. | Opaque to 005. 005 does not write, rank, retrieve, or change answer-card state. |
| Ledger-entry reference | 023 | Stable attempt/record ID and permitted timeline metadata when present in the fixture. | Opaque to 005. 005 does not write entries, classify exact/related matches, or implement ledger projection. |

## Correction impact set

016 FR-004 defines the complete affected-record types. The 005 correction scenario must link every category and show the returned canonical current-validity signal or linked reassessment:

1. Results
2. Claims
3. Check Results
4. Reports
5. Answer cards
6. Ledger entries
7. Source-skill versions
8. Use Assessments
9. Decision references

The correction-impact response, not a 005-owned traversal, supplies these references. The answer/use screen follows stable IDs through the committed reader and applies viewer access policy to each reference. Synthetic answer-card and ledger records are fixture inputs to verify link presentation, not feature-owned writes.

## Relationships

```text
Question / Plan revision / look exposure
                 │
                 ├── source skill version used (015)
                 ├── source window version ── correction relationship (016)
                 │          │
                 │          └── period comparison (016)
                 └── Execution → Result → Check Result → Claim revision
                                            │                 │
                                            └── report assertion (011)
                                                              │
                                Review (017) ──────────────────┤
                                                              └── Use Assessment (017)
                                                                      └── Decision (017)

Correction impact (016) links the nine affected record categories to new
current-validity/reassessment information while preserving prior revisions.
```

## Invariants

- Every relationship resolves to the exact committed record ID, revision, and digest where the frozen `EvidenceRef` contract applies.
- A new window and correction append linked records. No prior Result, Claim, report, skill version, Use Assessment, or Decision is overwritten.
- `reassessment_required`, reproduction status, Claim evidence, authorization, plan exposure, and window comparison are independent dimensions.
- An empty rationale cannot yield `authorized` or `risk_accepted`; a completed explicit negative authority decision may yield `denied`.
- Routine inconclusive or conflicting evidence can be answered honestly without silently becoming `supported`.
- A saved internal report does not create a publication authorization. An actual publication request is a distinct intended use.
- Viewer metadata and evidence bytes are checked against the current principal/grant. The demo does not claim filesystem isolation beyond the configured local boundary.
