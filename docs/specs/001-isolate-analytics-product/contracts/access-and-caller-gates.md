# Access and Caller Gate Contract

**Authority:** `docs/specs/access-and-judgment-contract.md` and
`docs/build/interfaces.md`. `src/analytics/principals.ts` is the one
feature-001 implementation of these gates.

## Principal and grant inputs

```ts
type PrincipalV1 = {
  schemaVersion: 1;
  principalId: string;
  authenticationMethod: string;
  roles: string[];       // loaded from project configuration
  projectId: string;
};

type SourceGrantV1 = {
  schemaVersion: 1;
  grantId: string;
  principalId?: string;
  roleId?: string;       // exactly one of principalId or roleId
  sourceId: string;
  operations: Array<'metadata' | 'aggregate' | 'row' | 'sheet' | 'export' | 'administration'>;
  audience: string;
  effectiveFrom: string;
  effectiveUntil: string;
  ownerRef: RecordRefV1;
  revision: number;
};
```

The authenticated identity is supplied by the local session/CLI boundary.
Roles and grants come from project configuration. Worker payload fields never
select the principal, grant, owner, or project. All inputs are validated;
missing, malformed, expired, future-effective, wrong-project, wrong-source,
wrong-operation, wrong-audience, or nonmatching grants deny.

## Required production caller surfaces

The same `authorizeSourceRequest` decision path must guard four consuming
surfaces. These are caller gateways, not four independent policy
implementations:

1. **Controller/context:** `buildAuthorizedWorkerContext` resolves the
   principal and filters source/catalog references before they enter the
   worker packet. It records allowed grant IDs and denies context creation if
   an identity needed by the request is absent.
2. **Source adapter:** `withAuthorizedSourceOperation(request, read)` checks
   the grant before invoking `read`. It returns a bounded denial that does not
   leak source paths, values, or schema details. Row, sheet, aggregate,
   metadata, export, and administration requests match only their own
   operation.
3. **Catalog:** `withAuthorizedCatalogQuery(request, search)` applies current
   access filters before candidate ranking and before summaries are exposed.
   Restricted entries cannot affect a visible rank or reveal a title.
4. **Viewer:** `withAuthorizedViewerRead(request, readMetadata, readBytes)`
   rechecks the current principal for both metadata and byte reads. A worker's
   previous grant is not reused as viewer authority; denial reveals neither
   protected bytes nor their path.

The 001 production launch composes these gateways: the context compiler
requests synthetic source metadata through the source adapter, resolves
authorized references through the catalog query port, and the inspection
path reopens its demo record through the viewer metadata and byte callbacks.
The acceptance fixture invokes that launch path with two configured
principals and synthetic local source/catalog/artifact providers. Assertions
cover every ungranted callback count remaining zero and explicitly granted
operations being allowed. Tests that only invoke the low-level grant
predicate, or test operation wrappers without wiring them into the context
and inspection flow, do not satisfy this contract. Later source/catalog/viewer
feature owners consume these exact gateways rather than defining alternate
grant rules.

## Guarantee label

Every context and inspection record states either `workflow-level` or a
host-enforced profile evidenced by the actual launch configuration. The
default local shell profile is `workflow-level`: the gates constrain product
callbacks and worker context, but a shell able to read the parent checkout is
not claimed to be filesystem-isolated. No label may say “inaccessible” solely
because a path was omitted from the packet.
