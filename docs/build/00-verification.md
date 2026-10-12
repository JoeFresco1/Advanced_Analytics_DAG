# Node 00 verification

**Scope:** documentation and graph/interface freeze only. No product behavior was implemented in this node.

## Declared checks

### Structural specification coverage

Command:

```powershell
python scripts/audit_spec_coverage.py
```

Result (exit 0):

```text
Audited 23 specs, 234 FRs, 114 edge cases.
All structural coverage references resolve.
```

This is the repository's structural map audit. It confirms each FR and Edge Case still resolves in the accepted spec/maps; it does not execute feature acceptance fixtures.

### Graph, ownership, and placeholder audit

Command (PowerShell here-string passed to the local Python interpreter):

```powershell
@'
import json, re
from pathlib import Path
root = Path('.')
graph = json.loads((root / 'docs/build/graph.json').read_text(encoding='utf-8'))
accept = (root / 'docs/build/acceptance-plan.md').read_text(encoding='utf-8')
trace = (root / 'docs/specs/traceability-audit.md').read_text(encoding='utf-8')
node_section = accept.split('## Graph-boundary acceptance register', 1)[1].split('## User acceptance paths', 1)[0]
node_rows = re.findall(r'^\|\s*([^|]+)\s*\|\s*([^|]*)\|', node_section, re.M)
node_rows = [(key.strip(), deps.strip()) for key, deps in node_rows if key.strip() not in {'Node', '---'}]
expected_deps = {node['key']: sorted(node.get('deps', [])) for node in graph['nodes']}
actual_deps = {key: sorted([] if deps in {'—', '-', ''} else [part.strip() for part in deps.split(',')]) for key, deps in node_rows}
assert actual_deps == expected_deps
trace_section = trace.split('## T-case ownership audit', 1)[1].split('The previous broad traces', 1)[0]
accept_section = accept.split('## Exact proposal T-case primary ownership', 1)[1].split('## Graph-boundary acceptance register', 1)[0]
row_re = re.compile(r'^\|\s*(\d{3})\s*\|\s*([^|]*)\|', re.M)
def owners(section):
    rows = {}
    for feature, cases in row_re.findall(section):
        cases = cases.replace('**', '')
        rows[feature] = sorted(re.findall(r'T\d{2}', cases))
    return rows
assert owners(trace_section) == owners(accept_section)
all_cases = [case for cases in owners(accept_section).values() for case in cases]
assert len(all_cases) == 61 and len(set(all_cases)) == 61
specs = sorted((root / 'specs').glob('[0-9][0-9][0-9]-*/spec.md'))
fr_count = sum(len(re.findall(r'^- \*\*FR-\d{3}\*\*:', p.read_text(encoding='utf-8'), re.M)) for p in specs)
edge_count = 0
for p in specs:
    text = p.read_text(encoding='utf-8')
    section = re.search(r'(?ms)^### Edge Cases\s*\n(.*?)(?=^## Requirements|\Z)', text)
    edge_count += sum(line.startswith('- ') for line in section.group(1).splitlines())
assert fr_count == 234 and edge_count == 114
index_section = accept.split('## Accepted FR and edge-case registers', 1)[1].split('## Exact proposal T-case primary ownership', 1)[0]
index_rows = re.findall(r'^\| \[(\d{3})\].*?\((\d+)\) \| (\d+) \|', index_section, re.M)
assert len(index_rows) == 23
assert sum(int(row[1]) for row in index_rows) == 234
assert sum(int(row[2]) for row in index_rows) == 114
markers = re.compile(r'TODO|FIXME|TBD|PLACEHOLDER')
outputs = [root / 'docs/build/ARCHITECTURE.md', root / 'docs/build/interfaces.md', root / 'docs/build/acceptance-plan.md']
assert not any(markers.search(p.read_text(encoding='utf-8')) for p in outputs)
print(f'Graph nodes: {len(node_rows)}/26, exact key and dependency match')
print(f'FR/edge coverage index: {len(index_rows)} features, {sum(int(row[1]) for row in index_rows)}/234 FRs and {sum(int(row[2]) for row in index_rows)}/114 edge cases')
print(f'T ownership: {len(owners(accept_section))} owners, {len(all_cases)}/61 unique; exact match to traceability audit')
print('Template marker scan: 0 matches')
'@ | python -
```

Result (exit 0):

```text
Graph nodes: 26/26, exact key and dependency match
FR/edge coverage index: 23 features, 234/234 FRs and 114/114 edge cases
T ownership: 18 owners, 61/61 unique; exact match to traceability audit
Template marker scan: 0 matches
```

The exact T-owner comparison confirms feature 019 owns T37, feature 021 owns T38, and feature 020 has neither in its primary list.

Graph file-output ownership command:

```powershell
$graph = Get-Content docs/build/graph.json -Raw | ConvertFrom-Json
$rows = foreach ($node in $graph.nodes) {
  foreach ($output in $node.outputs) {
    [PSCustomObject]@{ Node = $node.key; Output = $output }
  }
}
$duplicates = $rows | Group-Object Output | Where-Object Count -gt 1
"Graph output entries: $($rows.Count); unique output paths: $(($rows | Select-Object -ExpandProperty Output -Unique).Count); multiply owned exact paths: $($duplicates.Count)"
if ($duplicates.Count) { exit 1 }
```

Result (exit 0):

```text
Graph output entries after ownership/interface correction: 162; unique output paths: 162; multiply owned exact paths: 0
```

Shared configuration and interface files that are not separate graph output paths have their sole owners recorded in [interfaces.md](interfaces.md).

## Not run in Node 00

The final implementation commands from `00-plan.md`—`pnpm build`, `pnpm test`, `uv run --project python pytest`, and `pnpm smoke:viewer`—were not run. Node 00 changes no product code, has no analytics runtime or Python package yet, and those commands belong to implementation/release convergence. No runner or live worker was invoked. The build-stage instruction requires only the structural spec audit at this stage.

Node 001 creates the local neutral package and actual archive digest, tests the legacy compatibility adapter, and bootstraps Python wire contracts before 008 registration. These are implementation outputs, not an external publishing prerequisite. The Node 00 checks do not establish compatibility, runtime behavior, access enforcement, analytical support, evaluator isolation, or release readiness. Each requires the owner node's implementation and seeded end-to-end acceptance path.

## Architecture review rejection repair (2026-09-28)

The fresh reviewer rejected missing graph ownership for the neutral package, project-memory CAS/recovery, unnamed Plan schemas, absent CLI/controller extension seam, and an unowned descriptive calculator. These are now assigned in graph.json and frozen in interfaces.md. The early stage coordinator/fixture ownership also removes dependence on the later inquiry controller for slice A. No accepted spec, checklist, state vocabulary, reference answer or threshold changed.

Structural verification actually run in this correction: graph dependencies equal the acceptance register and are ordered/acyclic; all 162 output paths are unique; the package/dispatcher/vectors, descriptive calculator, extension registry and early stage files have named owners; all four Plan models and project-memory request/result/recovery functions are named; all 61 primary T owners still match the canonical audit; the three design outputs contain no unresolved markers. These checks verify document structure only. Fresh Luna xhigh review remains the semantic acceptance gate.
