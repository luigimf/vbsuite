# BE — VB Suite bench page

Backend specification for **SD-3503**.

---

## `GET /internal/vb-suite/benches/{id}`

Returns the master record plus the two engineer lists and the derived matrix.

```json
{
  "id": "vbs-m365",
  "name": "Microsoft 365 Productivity",
  "vendor": "Microsoft",
  "vendorLogoUrl": "…",
  "type": "Global",
  "status": "Published",
  "capacity": 160,
  "configuredRate": null,
  "coreSkills": ["M365", "SharePoint", "Teams Governance"],
  "delivers": ["…"],
  "outcomes": ["…"],
  "available": [
    { "engineerId": "e-1", "name": "Maria Borg", "email": "…", "rate": 62.50, "score": 4.6, "order": 1 }
  ],
  "recommended": [],
  "skillsMatrix": [{ "name": "Azure", "engineerCount": 4 }],
  "sharedClients": [{ "clientId": "c-1", "name": "Northmill Bank" }]
}
```

**Acceptance criteria**
- `available`/`recommended` are returned **in stored order**; `order` is contiguous from 1 per list.
- `skillsMatrix` is **derived at read time** from the engineers on the bench — never stored, never
  editable. Ordered by `engineerCount` descending, then name.
- `rate` and `score` are read from each engineer's live profile, not copied onto the bench.
- `sharedClients` is what makes delete and archive consequential; it is read-only here.

## Subscription rate

```
rate      = configuredRate > 0 ? configuredRate : max(available[].rate)
monthly   = capacity × rate
```

**Acceptance criteria**
- With no `configuredRate`, the **highest hourly rate on the Available list** sets the rate. The
  engineer holding it is flagged so the UI can mark **Sets rate**.
- `configuredRate` **overrides** it entirely; while set, no engineer is flagged.
- With an empty Available list and no override, the rate is **0** and the UI states the reason —
  never a hidden fallback rate.
- `configuredRate` accepts two decimals, must be > 0 when present, and is stored `null` when
  cleared (never `0`, never `""`).
- The multiplication happens **server-side on read** as well as client-side on slide, and the two
  must agree to the cent.

## Capacity — viewer-local, not a client commitment

**Acceptance criteria**
- `capacity` on the master record is the **default position** of the slider, nothing more.
- An internal operator moving the slider does **not** change what any client sees. Persisting it is
  optional and scoped to the operator's own view.
- On a manager dashboard the slider is that manager's own exploration; their movement is never
  written back to the master bench.
- The only rate input that travels with the master bench is `configuredRate`.

## Currency

**Acceptance criteria**
- The master figure is **EUR** on the internal dashboard.
- On a manager dashboard the same figure renders in the currency held on that manager's **client
  account** — label and formatting both derive from the viewing account, never a hard-coded EUR.
- No conversion is performed: the rate is expressed in the viewing account's currency, and one
  figure per currency is shown, never combined.

## Engineer lists

`vb_suite_bench_engineer` — `bench_id`, `engineer_id`, `list` (`available`|`recommended`), `order`.

**Acceptance criteria**
- An engineer appears **at most once per bench**, in exactly one list. Adding them to the other list
  is a **move**, not a copy — matching the drag between tabs.
- Reorder writes the whole list's `order` atomically; a partial reorder must not be observable.
- Removing an engineer affects this bench only — the engineer stays in the network, their profile
  untouched, and can be re-added.
- Removing the engineer who set the rate **recomputes the rate on the same response**, so the UI
  never shows a stale figure.
- The network search matches name and email, excludes nobody, and returns `onBench: true` for
  engineers already listed rather than filtering them out.
- Only **vetted** engineers are addable; the vetted badge is a property of the profile, not a flag
  set here.

## Real-time sync to manager dashboards

**Acceptance criteria**
- Every field on this page that a client can see propagates **immediately** to managers with access
  to a **Published** bench: content, engineer lists and order, type, vendor, logo, `configuredRate`.
- The manager mirror is **read-only**: the only interactive control on their side is the capacity
  slider. No manager write reaches this record.
- An engineer removed here disappears from the manager's list on the next push; a reorder here
  reorders theirs.
- Propagation is scoped by the client's access selection (SD-3504) — a bench excluded there does not
  reach that client even when Published.

---

## Reference

```
../SD-3502/BE.md               master record, status lifecycle, publish sync
../SD-3504/BE.md               per-client access resolution
../../ENGINEERING-BRIEF.md     BE-01 subscription record, BE-08 rate derivation
../../../prototype/index.html  → Internal Dashboard → V Benches → VB Suite → View V Bench
```

Repository: https://github.com/luigimf/vbsuite
