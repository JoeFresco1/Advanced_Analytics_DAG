# Contract: Data Measurement V1

## Purpose and compatibility

This contract defines the feature 007 evidence needed to identify data and reconcile measurement scope. It uses schemaVersion 1 record envelopes and shared RecordRefV1 references. It is not a replacement for the Pydantic PlanRegistrationV1, DescriptiveRequestV1, DescriptiveResultV1, AnalyticsRequestV1, AnalyticsResultV1, CheckResultV1, or commit contracts frozen in docs/build/interfaces.md. Feature 007 must consume those owners and their generated schemas unchanged.

Unknown JSON fields fail at authoritative ingress. Required facts are present; unavailable semantic facts use the canonical unknown sentinel rather than an invented default. A reference to bytes still requires current access authorization.

## Source/data version

A 007 acquisition version binds all of these dimensions:

- stable source ID and immutable version RecordRefV1;
- expected/observed source role, separate from the grant's principal/role;
- grant reference, allowed operation, audience, effective time and retrieval scope, without copying credentials;
- acquisition time and the actual source-provided consistency evidence;
- a content digest or source-native immutable version reference when available;
- schema ref and independent semantic refs for keys, units/currency, timezone, categories, population, and business definitions;
- window/event/processing-time/watermark/freshness facts when available;
- current availability and replay availability, each reasoned independently;
- unknown prior-origin, version, consistency, grain, cardinality, definition, or freshness facts where they are genuinely unavailable.

A query string and query timestamp do not establish repeatable snapshot semantics. An exact-snapshot label requires a source-native version or immutable retained content and source evidence sufficient for that claim. A query-time-only source records its limited consistency and explicit unknown where appropriate.

## Transformation declaration and manifest

The transformation declaration must be immutable and committed before execution. It names exact input version refs; expected input grain and entity keys; expected join/key cardinality; declared output grain; intentional grain changes and their denominator meaning; joins and unmatched behavior; population/time cutoffs; exclusions and missingness; and required unit/category/freshness/maturity definitions.

The resulting transformation manifest references that declaration and records the exact output version, producing execution and code/environment identity, actual input/output grain and keys, and Python-produced row/entity/join/exclusion counts. It does not overwrite the declaration with observed facts.

For a new transformation on a legacy version with unknown grain/cardinality, the declaration is mandatory and the observed key/count validation must pass before its dependent measurement can be supportable. The legacy source retains unknown metadata.

Expected cardinality is checked against observed entity-key multiplicity. A declared one-to-many join can increase rows and remain valid when the declared output grain explains the expansion, distinct-entity counts and denominator meaning reconcile, and no unexplained exclusion or unmatched condition remains. An over-cardinality join or missing declaration creates a support-blocking finding.

## Measurement and denominator contract

Every result names its statistic, measurement definition, unit, population, exact period, denominator definition/unit, group meaning if any, missingness/exclusion rule, and exact ordered source/transformation refs. Its denominator lineage records the eligible source denominator and each transformation's declared and observed row/entity counts, join effects, exclusions, unmatched keys, and output denominator.

- Count uses the observation unit and eligibility rules declared for that count.
- Sum uses the declared measurement unit and does not silently mix or convert currencies.
- Mean uses the declared unit and only the eligible denominator.
- Proportion binds numerator definition and denominator definition to the same named population and period.

Python computes the numerical values and all numerical diagnostics from the admitted data. TypeScript may validate schemas, digests, identities, references and consistency of supplied evidence; it must not produce a numeric summary or substitute expected fixture values.

For a zero denominator, count may be zero only when the requested count is itself defined as zero. Mean and proportion values are absent/null with denominator zero and an explicit reason. Non-finite values are rejected. Missing and excluded observations remain separately counted and reasoned; hidden exclusions are not treated as ordinary missingness.

A group requires an exact group/category definition reference. If its meaning is unavailable, report the raw observed category key only when permitted, emit the group-meaning finding, and block support for the semantic group claim. Never infer sensitive or business labels from names or distributions.

## Check findings and support boundary

007 finding outcomes use only canonical check outcomes: pass, fail, inconclusive, not_applicable, or error. A missing or stale finding is a separate coverage condition owned by 010. An unjustified not_applicable does not pass. Every finding identifies its owning check, exact subjects, observed/expected evidence, reason, and limits.

The 007 supportBlockers list is evidence for the dependent controller/check consumer. Feature 007 does not create CheckResultV1 records or assign Claim, authorization, or global supported-presentation states. Feature 003 selects this mode; feature 010 applies the frozen policy and blocks a dependent Claim when a required measurement finding fails, is inconclusive/error, or is missing/stale. No finding or valid negative calculation initiates a retry to obtain a favorable value.

## Freshness, maturity, access and semantic drift

Freshness checks compare observed source/window facts to an exact declared expectation and recorded policy reference. The feature introduces no global time threshold. If either required freshness or semantic meaning is unknown, the comparison remains qualified/unassessed and cannot be represented as an unqualified current comparison.

Late events record event time separately from processing time and watermark, remain bound to their source version, and produce a new version/correction link. Immature labels are excluded by the declared maturity rule or marked unavailable; affected cohort counts and the reason are returned.

The source grant is checked by 001 before metadata or rows are exposed. Expected and actual source roles are compared, and a mismatch fails closed with access-safe evidence. A 007 finding never grants a new operation. Local shell access outside this boundary remains only a workflow-level guarantee.

Schema, unit, timezone, key, category, or population meaning changes create new references even if schema bytes are identical. Comparison requires exact known semantic compatibility or an explicit, versioned alignment transformation. Historical results and old meanings remain inspectable, and dependent current assessments require reassessment.

