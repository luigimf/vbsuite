# BE — Manager Technology Capacity Catalogue page (view only)

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in UI copy; the manager-facing page title and profile drop-down option read **Castillians® Capacity Catalogue**. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Backend specification for **SD-3506**.

---

## `GET /manager/technology-capacity-catalogue`

Query: `type` (`All` default).

```json
{
  "clientCurrency": "EUR",
  "benches": [
    {
      "id": "vbs-m365",
      "name": "Microsoft 365 Productivity",
      "vendor": "Microsoft",
      "vendorLogoUrl": "…",
      "type": "Global",
      "capacity": 160,
      "rate": 62.50,
      "coreSkills": ["M365", "SharePoint", "Teams Governance"],
      "delivers": ["…"],
      "outcomes": ["…"],
      "skillsMatrix": [{ "name": "Azure", "engineerCount": 4 }],
      "available": [{ "engineerId": "e-1", "name": "Maria Borg", "order": 1 }],
      "recommended": []
    }
  ]
}
```

**Acceptance criteria**
- The response contains **no `status` field**. Status is an internal concept; the manager receives
  only what they are allowed to see, already filtered.
- **`rate` is the resolved subscription rate** — `configuredRate` when set, otherwise the highest
  rate on the Available list (SD-3503). **Per-engineer rates are never serialised to this endpoint.**
- `clientCurrency` comes from the manager's **client account** and drives every figure's label and
  formatting. No conversion is performed.
- Engineer entries carry profile-display fields only — name, avatar, score, review count,
  availability, seniority, country, time-zone group, skill ratings, badges, profile link. **No rate,
  no internal notes.**
- Lists are returned in the **order set internally**; the manager cannot reorder.
- `skillsMatrix` is derived at read time, as internally.
- `type` filters the returned set; `All` returns everything visible. Filtering is server-side.

## Visibility resolution

Applied on **every** request, in this order:

```
1. entitlement check (SD-3505)                → 403 if not entitled
2. benches = status == Published              → nothing else is ever eligible
3. if (!access.allBenches) ∩ access.benchIds  → the client's selection
```

**Acceptance criteria**
- **Only `Published` benches are ever returned.** `Draft` and `Archived` are invisible here — an
  unpublished or archived bench must not appear even if it is named in `benchIds`, and even if it
  was visible a moment ago.
- **A newly created and published bench appears automatically** for every client whose *Virtual
  Benches to be shown* is left on the default (all benches) — because "All" is stored as a flag, not
  a snapshot (SD-3504). No per-client action, no re-login.
- Unpublishing or archiving a bench **withdraws it immediately**, mid-session.
- A bench removed from this client's selection is withdrawn the same way.
- The distinction between **403 (no access)** and **200 with an empty list (access, nothing
  published)** is preserved — the two produce different screens.

## Real-time sync

**Acceptance criteria**
- The page is a **live mirror** of the master benches: content, type, vendor, logo, core skills,
  delivers, outcomes, engineer lists and their order, and the resolved rate all update **without a
  re-publish step and without a reload**.
- Delivery is at-least-once and **idempotent** on the client: a duplicate push must not duplicate a
  bench in the list.
- A push that removes the bench currently being viewed resolves to the index with a
  "no longer available" state — never a blank page and never a stale detail view.
- **No manager action writes back to the master bench.** The capacity slider is viewer-local and is
  persisted, if at all, only against that manager's own view.
- Ordering of the index is stable between pushes so cards do not reshuffle under the reader.

## Enquiry submission

`POST /manager/technology-capacity-catalogue/{benchId}/enquiries` — `capacityHours`, `preferredStart`,
`note`.

**Acceptance criteria**
- `capacityHours` must be one of the offered tiers (**40 / 80 / 160 / 500 / 1000**);
  `preferredStart` is required and must not be in the past. Both are validated server-side.
- The enquiry **creates nothing** — no bench, no subscription, no capacity commitment, no charge.
  It is a notification with a record.
- Records the manager, their client, the bench and the submitted values, so Human Capital can act on
  it and it is auditable.
- Rejected submissions return field-level errors the form can attribute.

## Email notifications in this flow

- Submitting an enquiry emails the **manager a receipt** and **Human Capital a notification**.
- **Templates live in the Email Notifications epic** — awareness only; this story triggers them and
  supplies the data, and defines neither.

---

## Reference

```
../SD-3505/BE.md                        entitlement and route guard
../../internal-dashboard/SD-3502/BE.md  master record, status lifecycle, publish sync
../../internal-dashboard/SD-3503/BE.md  rate derivation and engineer lists
../../internal-dashboard/SD-3504/BE.md  client_vb_suite_access and access resolution
../../../prototype/index.html           → Manager Dashboard → Technology Capacity Catalogue
```

Repository: https://github.com/luigimf/vbsuite
