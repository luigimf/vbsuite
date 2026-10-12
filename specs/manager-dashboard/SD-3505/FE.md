# FE — Manager menubar & profile drop-down: Technology Capacity Catalogue access

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in UI copy; the manager-facing page title and profile drop-down option read **Castillians® Capacity Catalogue**. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Angular handoff for **SD-3505**. The menubar on the logged-in **manager** dashboard is redesigned so
managers granted access reach the suite from their profile drop-down, at **/technology-capacity-catalogue**.

> ### ⚠ Not in the prototype
>
> The dark **Internal Dashboard / Manager Dashboard** toggle in the Master Technology Capacity Catalogue prototype is a
> prototype navigation device and **must not be built**. This story's UI is specified in Figma:
> [menubar & profile drop-down](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=11613-8085&t=aTJFhDRsi2oAclPH-11).

---

## Scope

- The manager menubar and its logic.
- A new **Castillians® Capacity Catalogue** option in the **profile drop-down**.
- The route **`/technology-capacity-catalogue`**, which renders the page specified in SD-3506.

## Profile drop-down

- New option with the heading **Castillians® Capacity Catalogue** — the registered mark is part of
  the heading — and, beneath it, the description **"View our latest technology capacity
  catalogue."** Type, spacing and states per the Figma frame (updated 12 Oct).
- Placement, order, iconography, dividers and hover/active treatment per
  [Figma](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=11613-8085&t=aTJFhDRsi2oAclPH-11). Existing options are not reordered beyond what that frame shows.
- Navigates to `/technology-capacity-catalogue` in the same tab.
- The option is **rendered only for managers with access**; for everyone else it is **absent** —
  never present-and-disabled, and never a dead link.

**Acceptance criteria**
- A manager **with** access sees the option and lands on the suite page.
- A manager **without** access does not see the option at all.
- Access is decided **server-side** (SD-3504) and delivered with the session/menu payload; the
  client never computes eligibility from a client or bench list it happens to hold.
- The heading reads **Castillians® Capacity Catalogue** and the description **"View our latest
  technology capacity catalogue."**, both verbatim; each truncates with an ellipsis rather than
  wrapping if the drop-down is narrowed.

## Route

- **`/technology-capacity-catalogue`** — the suite index (SD-3506). A bench detail view is a child of it, so
  **Back to Technology Capacity Catalogue** returns without a full reload.
- Guarded: a manager without access who reaches the URL directly gets the platform's standard
  **not-authorised** treatment and is **not** shown an empty suite page.
- The route survives a refresh and is shareable between two managers of the same client, subject to
  the same guard.
- Deep-linking to a bench a manager may no longer see (unpublished, archived, or removed from their
  client's selection) resolves to the index with the standard "no longer available" treatment.

## Menubar logic

**Acceptance criteria**
- The menubar is driven by the **entitlements in the session payload**, not by hard-coded role
  checks scattered per component — this option is the first of several that vary per client.
- Losing access mid-session (an operator switching **Technology Capacity Catalogue Access** to No, or deselecting this
  manager) removes the option and blocks the route **without a re-login**, on the next menu
  resolution.
- No layout shift in the menubar between a manager with access and one without, beyond the option's
  own presence in the drop-down.

---

## Reference

```
../SD-3506/                    the page this option opens
../../internal-dashboard/SD-3504/   where access is granted
```

Figma: [menubar & profile drop-down](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=11613-8085&t=aTJFhDRsi2oAclPH-11)

Repository: https://github.com/luigimf/vbsuite
