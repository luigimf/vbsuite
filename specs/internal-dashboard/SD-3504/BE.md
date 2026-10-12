# BE — Manage Client modal: Cobranded field move + Technology Capacity Catalogue Access

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in all UI copy. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Backend specification for **SD-3504**.

---

## 1. Cobranded Client — no backend change

**Acceptance criteria**
- The field's **storage, name, type and semantics are unchanged.** This is a UI placement change
  only; no migration, no new column, no altered payload.
- If the Manage Client payload groups fields by box, the field moves group — the value itself is
  untouched and an existing client's saved value must read back identically.

## 2. Technology Capacity Catalogue access record

`client_vb_suite_access` — one row per client.

| Field | Notes |
|---|---|
| `clientId` | Primary key |
| `enabled` | Boolean, defaults **false** |
| `allManagers` | Boolean, defaults **true** |
| `managerIds[]` | Meaningful only when `allManagers` is false |
| `allBenches` | Boolean, defaults **true** |
| `benchIds[]` | Meaningful only when `allBenches` is false |
| `updatedAt`, `updatedBy` | Audit |

**Acceptance criteria**
- **"All" is stored as a flag, never as an enumerated list of today's ids.** This is the whole
  mechanism by which a newly published bench reaches every client on the default — persisting
  `benchIds` of everything that exists at save time would silently break it.
- Setting a subset writes `allBenches: false` with the explicit ids; returning to select-all writes
  `allBenches: true` and **clears** `benchIds`.
- `enabled: false` retains `managerIds`/`benchIds` so re-enabling restores the prior selection.
- `managerIds` are validated to belong to **this client organisation**; a foreign id is rejected,
  not ignored.
- `benchIds` are validated to exist; ids that are not currently Published are accepted and stored
  (a bench may be re-published later) but do not grant visibility while unpublished.
- Rejected with a validation error: `enabled: true` with `allManagers: false` and an **empty**
  `managerIds`, or `allBenches: false` and an **empty** `benchIds`.

## Access resolution

The benches a given manager sees:

```
if (!access.enabled)                               → none
if (!access.allManagers && !managerIds.has(me))    → none
benches = published(vb_suite_bench)
if (!access.allBenches)  benches = benches ∩ benchIds
```

**Acceptance criteria**
- Resolution happens **server-side on every request**. A manager's client cannot be trusted to
  filter its own list.
- `published(...)` is applied **first and always**: an id in `benchIds` whose bench is Draft or
  Archived yields nothing.
- The manager and bench conditions compose as **AND**.
- A manager with no access receives an empty list **and** the Technology Capacity Catalogue entry point is
  withheld — not an empty page they can navigate to.

## Propagation

**Acceptance criteria**
- Saving this box takes effect **in real time** for every affected manager session — a bench
  granted or withdrawn here appears or disappears without a re-login.
- Publishing a new bench (SD-3502) reaches every client with `allBenches: true` with **no write to
  this record**.
- Archiving or deleting a bench withdraws it from every client, regardless of `benchIds`.
- A manager removed from the organisation loses access immediately, whatever `allManagers` says.
- Every change to this record is audited with actor, timestamp and the before/after selection —
  this is a client-visibility control and needs to be answerable after the fact.

---

## Reference

```
../SD-3502/BE.md               master bench record, status lifecycle, publish sync
../SD-3503/BE.md               what propagates to a manager once access is granted
```

Repository: https://github.com/luigimf/vbsuite
