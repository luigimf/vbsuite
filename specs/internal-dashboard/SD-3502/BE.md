# BE — Technology Capacity Catalogue tab (master bench index)

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in all UI copy. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Backend specification for **SD-3502**.

---

## Master bench record

`vb_suite_bench` — the **master template**. It is not a subscription and it is not tied to a
client.

| Field | Notes |
|---|---|
| `id` | Server-issued |
| `name` | Required, unique per suite, trimmed |
| `type` | `Global` \| `Data Regulated` — required |
| `vendor` | Optional free text |
| `vendorLogoUrl` | Optional; SVG preferred, raster accepted |
| `status` | `Draft` \| `Published` \| `Archived` — defaults to `Draft` |
| `coreSkills[]` | ≥ 1, resolved against the platform skills library |
| `delivers[]` | ≥ 1 ordered lines |
| `outcomes[]` | 0+ ordered lines |
| `capacity` | Default hours shown on first load of the bench page; **not** a client commitment |
| `configuredRate` | Optional hourly override — see SD-3503 |
| `available[]`, `recommended[]` | Ordered engineer references — see SD-3503 |
| `createdAt`, `updatedAt`, `updatedBy` | Audit |

**Acceptance criteria**
- `name`, `type`, ≥1 `coreSkills` and ≥1 `delivers` are enforced **server-side**; the client-side
  check is a convenience, never the contract.
- Blank and whitespace-only lines in `delivers`/`outcomes` are stripped before persisting.
- Line order is preserved exactly as authored — these are read as a value proposition.
- `capacity` is optional at creation and defaults to a named constant, not 0.

## `GET /internal/vb-suite/benches`

Query: `q`, `status` (`All` default).

```json
[
  {
    "id": "vbs-m365",
    "name": "Microsoft 365 Productivity",
    "vendor": "Microsoft",
    "vendorLogoUrl": "…",
    "type": "Global",
    "status": "Published",
    "capacity": 160,
    "coreSkills": ["M365", "SharePoint", "Teams Governance"],
    "delivers": ["…"],
    "outcomes": ["…"],
    "engineerCount": 8,
    "engineerAvatars": [{ "name": "Maria Borg", "vetted": true }],
    "sharedClientCount": 3
  }
]
```

**Acceptance criteria**
- `q` matches **name and core skills**, case-insensitive, substring.
- `status` filters exactly; `All` returns every status. The filter is applied server-side —
  never by hiding rows the client already holds.
- `engineerCount` counts the **Available** list only; avatars are capped and ordered as stored.
- `sharedClientCount` is informational — it is what makes an archive or delete consequential.
- Internal roles only. A manager token on this endpoint is **403**, not an empty list.

## `POST` / `PATCH /internal/vb-suite/benches/{id}`

**Acceptance criteria**
- `POST` returns the created bench with `status: "Draft"`. Status is never settable at creation.
- `PATCH` is partial; an omitted array is untouched, an empty array **clears** it (subject to the
  ≥1 rules, which reject clearing `coreSkills`/`delivers`).
- Every write stamps `updatedAt`/`updatedBy` and appends an audit record.
- Two operators editing the same bench: last write wins **per field**, and the response returns the
  merged record so the second operator's screen reconciles.

## Status lifecycle and publish sync

```
Draft      → visible to internal only
Published  → visible to every client granted access (SD-3504), on their manager dashboard
Archived   → withdrawn from manager dashboards; retained internally with its history
```

**Acceptance criteria**
- **Only `Published` benches sync to manager dashboards.** `Draft` and `Archived` never appear,
  whatever a client's access selection says.
- A status change takes effect **in real time** — no publish job, no cache to wait out. A manager
  with the page open sees the bench appear or disappear on the next push or poll cycle.
- Archiving is **not** deletion: work already demonstrated against it stays readable internally.
- `Draft → Published` re-validates the full record. A bench that lost its last `delivers` line
  cannot be published.
- Every status transition is audited with actor and timestamp.

## Real-time propagation

**Acceptance criteria**
- Content edits to a **Published** bench propagate to every client with access **without a
  re-publish step** — the manager dashboard is a live mirror of the master, not a copy.
- Propagation covers name, type, vendor, logo, core skills, delivers, outcomes, engineer lists and
  the rate inputs. It does **not** carry the internal operator's slider position (SD-3503).
- A newly created bench, once **Published**, appears automatically for every client whose access
  selection is left on **all benches** (SD-3504) — no per-client action.
- Delivery is at-least-once and idempotent on the client side; a duplicate push must not duplicate
  a bench in the manager's list.

## Delete

**Acceptance criteria**
- Delete is permanent and removes the master bench with its skills mix, value proposition and
  engineer lists.
- Clients it was shared with **lose access immediately**.
- Blocked with a clear error if the bench is referenced by a live subscription; archive instead.

---

## Reference

```
../SD-3503/BE.md               rate, capacity and engineer-list rules
../SD-3504/BE.md               per-client access resolution
../../../prototype/index.html  → Internal Dashboard → V Benches → Technology Capacity Catalogue
```

Repository: https://github.com/luigimf/vbsuite
