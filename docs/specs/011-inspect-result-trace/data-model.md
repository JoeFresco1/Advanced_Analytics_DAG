# Data Model: Numerical report bindings and browser evidence trace

This feature defines read-side presentation projections over committed
records. These projections do not replace Claim, Result, CheckResult, Plan,
Execution, artifact, SourceGrant, or manifest records owned elsewhere.

## ReportViewV1

The answer-first projection for one committed inquiry.

| Field | Meaning and validation |
| --- | --- |
| schemaVersion | Literal version 1. |
| inquiryRef | Stable committed inquiry EvidenceRef. |
| question | Original question text, rendered as text. |
| answer | Scoped answer or non-answer text, rendered as text. |
| population | Explicit first-screen population description or unknown/missing marker. |
| period | Explicit time scope or unknown/missing marker. |
| materialAssumptions | Structured list of consequential assumptions. |
| uncertainty | Statistical and methodological uncertainty with its meaning. |
| limitations | Remaining limitations, including unresolved evidence and access. |
| interpretation | Claim wording that distinguishes prediction, association, and causation without expanding its evidence. |
| assertions | ReportAssertionV1 values in report order. |
| charts | ReportChartV1 values in report order. |
| firstScreenCompleteness | Computed required-field list, missing field names, and complete boolean. It is complete only when answer, population, period, material assumptions, uncertainty, and limitations are all present. |
| evidenceAvailability | Current safe summary of linked record/artifact availability; never a blanket verified state. |
| sourceBoundary | Actual boundary label, including workflow-level when local shell access can bypass the product surface. |
| linkedProvenance | Access-safe LinkedProvenanceV1 refs reachable from the committed inquiry/assertions, including missing or restricted relationships. |

## ReportAssertionV1

An individual displayed numerical assertion.

| Field | Meaning and validation |
| --- | --- |
| assertionId | Stable ID within the committed report. |
| text | Optional connective wording; never the authority for the number. |
| claimRef | Exact committed Claim EvidenceRef. |
| resultRef | Exact committed Result EvidenceRef. |
| selector | Exact stable selector into the Result payload. The selected value must exist and be numeric. |
| unroundedValue | Canonical numeric value found at selector, retained for inspection. |
| unit | Must equal the selected Result measurement unit. |
| denominator | Definition and exact denominator selector/value or explicit not-applicable reason from the Result. |
| rounding | Declared deterministic presentation mode and digits; must match the report's allowed presentation rule. |
| displayValue | Derived by the report resolver from unroundedValue and rounding, not accepted as worker-supplied authority. |
| scope | Population and period refs/labels matching the Claim and Result. |
| uncertainty | Estimate/interval or other uncertainty data and explicit definition, bound to the Result. |
| checkRefs | Exact current CheckResult and policy refs applicable to this Claim. |
| traceRefs | Execution/attempt, Plan, transformation, source/data version, program/environment, and output refs required by the frozen evidence binding. |
| linkedProvenance | Generic committed LinkedProvenanceV1 refs reachable from this assertion, in addition to inquiry-level refs. |
| bindingAssessment | Mechanical selector/value/unit/denominator/scope/rounding comparisons with mismatch reasons. |
| presentationEligibility | Derived from the current committed claim assessment and check coverage; not inferred from task exit or a report-authored flag. |

## ReportChartV1

An individual chart specification with evidence lineage.

| Field | Meaning and validation |
| --- | --- |
| chartId | Stable ID within the report. |
| claimRef | Claim whose interpretation the chart supports or illustrates. |
| resultRef and selector | Exact committed source Result and selected data. |
| aggregation | Declared source aggregation and its bound transformation/result reference. |
| axes | Field selectors, labels, units, scale type, and displayed range. |
| filters | Declared filter selectors/values and population effect. |
| labels | Legend, category, and series labels. |
| uncertainty | Definition and visual encoding for intervals/bands/error bars, or explicit absence with reason. |
| bindingAssessment | Changed aggregation, scale, filter, label, or source selector is visible and blocks supported presentation where it invalidates the claim. |

## TraceItemV1 and EvidenceRef

The browser-facing ordered chain is ReportAssertion → Claim → Result →
CheckResult/Review → Execution/Attempt → Plan → program/environment pins →
Transformation → Dataset version. Every reference keeps the frozen
EvidenceRef record ID, revision, and digest. Each item exposes only fields
permitted to the current reader.

The projection reports canonical Artifact availability (available,
unavailable, or redacted), required-check outcome/coverage, claim evidence
state, and reproduction state using their existing vocabularies. A missing
reference is represented as a missing link in the trace with a safe reason;
a changed digest/revision is shown as a binding mismatch. Do not add a generic
verified state or reinterpret failed, null, negative, conflicting,
inconclusive, unsupported, or incomplete work as a process failure.

## LinkedProvenanceV1

This generic read projection displays typed committed relationships already
reachable from the inquiry and its assertions. It does not create records or
duplicate the owners' lifecycle state. A relationship has a discriminator, an
exact `RecordRefV1` (`recordId`, `revision`, `digest`), a bounded access-safe
label, and a visible link condition. The supported relationship discriminators
are `source_version`, `skill_version`, `independent_validation`,
`authority_decision`, `supplied_reference`, `executed_recipe`, and
`outcome_exposure`. A skill link may show its canonical current skill status;
the projection must not invent an additional skill state.

Resolve each exact reference only through committed manifests. Verify that
the current record has the linked revision and digest; changed, missing, or
uncommitted targets remain visibly unavailable and never become supported
provenance. Apply the current principal's grant to the record's source scope
before returning metadata. The response may show the exact opaque reference
and a bounded `missing`, `changed`, or `restricted` label, but never raw source
text, protected titles or paths, protected values, or an uncommitted record's
content. The viewer renders relationship labels and permitted fields as text.

The acceptance fixture commits synthetic records for every relationship type
and exercises the answer and trace APIs plus the browser. It does not depend
on the later production providers in 014/022. Paired source-boundary records
exercise SC-008: the workflow-readable fixture displays `workflow-level`; the
separately permissioned fixture displays `independently permissioned source`.
Both paths assert zero ungranted raw values and restricted paths in viewer
responses.

## Access projections

| Projection | Required authorization |
| --- | --- |
| Inquiry/Claim shell | Returns the same empty static page; no record metadata. |
| Answer or trace metadata | Current Principal plus current metadata grant for every source scope disclosed. Denial reveals no protected title, value, or path. |
| Aggregated evidence/artifact metadata | Current Principal plus matching metadata or aggregate operation, as declared by the artifact's scope. |
| Raw/source artifact bytes | Current Principal plus the declared row/export/sheet operation for the source and audience. Check before reading or returning bytes. |

The trace may disclose that a link is restricted or unavailable without
disclosing its protected path, title, or value.
