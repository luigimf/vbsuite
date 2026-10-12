# BE — Manager menubar & profile drop-down: Technology Capacity Catalogue access

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in UI copy; the manager-facing page title and profile drop-down option read **Castillians® Capacity Catalogue**. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Backend specification for **SD-3505**.

---

## Entitlement in the session payload

The menu must not be assembled from data the client guesses at. Add an explicit entitlement.

```json
{
  "entitlements": {
    "virtualBenchSuite": { "enabled": true, "route": "/technology-capacity-catalogue" }
  }
}
```

**Acceptance criteria**
- `virtualBenchSuite.enabled` resolves per **authenticated manager**, from
  `client_vb_suite_access` (SD-3504): the client's access must be enabled **and** this manager must
  be in scope (`allManagers` true, or listed in `managerIds`).
- It is `true` even when the client's **bench** selection currently resolves to an empty set — a
  client with access but nothing published yet still reaches the page and sees its empty state. The
  entitlement is about the page, not its contents.
- Computed server-side on every session/menu read. The client never derives it.
- A manager removed from the organisation resolves `false` immediately, whatever `allManagers` says.

## Route guard

**Acceptance criteria**
- `GET /technology-capacity-catalogue` data endpoints re-check the entitlement **per request**. A menu that
  was rendered a minute ago is not authorisation.
- An unauthorised request returns **403**, never an empty 200 — an empty list and "not allowed" must
  be distinguishable by the client.
- Revocation takes effect on the **next request**, with no re-login and no cache to wait out.
- Every 403 on this route is logged with actor and client, so a support question about "I can't see
  the suite" is answerable.

---

## Reference

```
../SD-3506/BE.md                    the suite payload this route serves
../../internal-dashboard/SD-3504/BE.md   client_vb_suite_access and access resolution
```

Repository: https://github.com/luigimf/vbsuite
