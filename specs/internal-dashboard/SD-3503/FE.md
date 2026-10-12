# FE — Technology Capacity Catalogue bench page

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in all UI copy. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Angular handoff for **SD-3503**. The page **View V Bench** opens from a card on the Technology Capacity Catalogue tab:
the full master record for one bench, and the only place its content, rate inputs and engineer
lists are edited.

> ### ⚠ The prototype's menubar is not part of this work
>
> The dark **Internal Dashboard / Manager Dashboard** toggle in the prototype's header exists
> **only** so one file can demonstrate both dashboards. It is a prototype navigation device and
> **must not be built.**
>
> **Keep the platform's existing menubar as it is.**

---

## Frame

- Replaces the tab body in place — same page, same tab row, no route change in the prototype;
  build it as a child route of the Technology Capacity Catalogue tab.
- **Back to all** — arrow + label, body 13px/600 `var(--gray-700)`, darkens to ink on hover.
- Sections are stacked `Card`s, 24px apart, each with a 52px section glyph, a Bricolage 26px/700
  title and a 14px `var(--gray-700)` sub-line. Section headers stack vertically below 720px.

## Head card

- Vendor lockup (25px logo tile + vendor name, Bricolage 17px/700 uppercase `#C29A65`) and the
  **type tag** on one row, 18px gap, wrapping.
- Bench name as `h2`, Bricolage **42px**/700 (30px below 720px).
- Right, `flex: none`: **status Select** (150px, 45px) and a **Delete** button (45px, trash glyph,
  `#F8F8F8` fill, 2px `#E5E5E5`, hover `#F2F2F2`).

## Details card

`repeat(auto-fit, minmax(230px, 1fr))`, 20px gap, `align-items: end`:

- **Vendor** — Input
- **Bench Name** — Input
- **Bench Type** — Select (Global / Data Regulated)

Each writes to the master bench **on change**; there is no Save button on this card. The same
values are editable in the modal (SD-3502) — both paths write the same fields.

## Skills Mix

- **Core skills** — uppercase 12px/700 label + chips (1px `var(--gray-150)`, `var(--gray-50)`).
- **Edit content** — outline secondary, 35px, pinned bottom-left (`margin-top: auto`), opens the
  modal from SD-3502.

## Skills Matrix

- Aggregated skills **across the engineers on this bench**, each chip showing the skill and the
  number of engineers holding it (Bricolage 11px/700 in `--freq-fg`).
- Ordered by frequency descending, then alphabetically.
- Collapsed by default to the first row-set; **See more / See less** appears only when there is
  overflow.
- Empty: "Add engineers below and their skills aggregate here."
- **Read-only** — it is derived, never authored.

## What It Delivers · Outcome

- **What It Delivers** — 4px bullet list, body 15px/150%.
- **Outcome** — inset box `#FCFFFD`, 2px `#B9FFD1`, 8px radius, 10px padding, each line prefixed
  with `check-circle-green.svg` (19px).
- Both carry **Edit content** to the modal.

## Monthly Engineering Hours

An **explorer**, not a client setting.

- Slider: `min 40`, `max 1000`, `step 10`; borderless 8px `#ECECEC` track, ink thumb, thumb
  scales 1.08 on hover. End labels **40 hrs** / **1000 hrs**.
- **Capacity Plan** — uppercase 12px/700 label + Bricolage 34px/700 value, `{n} hrs`.
- **Subscription Rate (EUR)** — same treatment; `capacity × rate`, formatted `€0,000.00`.
- An **info dot** beside the heading (18px, `#C9C9C9`, hover `#9C9C9C`) reveals on hover/focus:
  "Shown in EUR on the internal dashboard. On a manager's dashboard the same rate is shown in that
  client's own currency."
- Block is 595px wide, capped at 720px; full width and auto height on mobile.
- The slider position is **local to the viewer** — see Integration & sync.

## Configured Rate

- Sits in the Engineers card control row: 50px, 2px `var(--border)`, uppercase 11px/700 label,
  72px numeric input, clear (✕) button once set.
- Placeholder is the **highest engineer rate** on the Available list, so the default is visible
  without being typed.
- Accepts digits and one decimal point only.
- Clearing it returns the bench to "highest engineer rate sets the rate" — tooltip on the clear
  button reads "Use the highest engineer rate".

## Engineers

- Tabs **Available (n)** / **Recommended (n)** — Bricolage 20px, 700 active, white card when
  active. Horizontal scroll, never wrap, below 720px.
- **Engineers can be dragged from one tab to the other**: the tab highlights as a drop target and
  the engineer moves lists on drop, switching the view to the destination tab.
- **Search the network** — 50px field, matches name and email, clear ✕. Results panel: avatar,
  name, email, rate, score, and **Add** — or a plain "on this bench" label when already listed.
  Adds land on the tab currently open. Empty: "No engineer matches that name or email."
- Row: drag handle (6-dot grip) + **rank number** · 44px avatar with vetted badge · name (17px/600)
  + email · rating bars + score (16px/700) · **Rate** (uppercase label + 18px/700 value) ·
  **Sets rate** tag when this engineer's rate is the one driving the subscription rate · remove ✕
  (35px).
- Rows are **drag-to-reorder** within a list; the rank number is the priority order the client sees.
- Row lifts with `0 8px 20px rgba(0,0,0,.1)` while dragging; cursor `grab`.
- Removing opens a confirm modal: "Takes **{engineer}** off the {list} list for **{bench}**. They
  stay in the network and can be added back at any time."
- Empty copy differs per tab — Available names the rate consequence, Recommended names the
  priority order.
- Rows wrap below 1024px; the row never scrolls horizontally.

## Delete bench

Confirm modal (max 520px): "This permanently removes **{bench}** from the Technology Capacity Catalogue, with its skills
mix, value proposition and engineer lists. Clients it has been shared with lose access to it."
Danger CTA + Cancel. On success, return to the Technology Capacity Catalogue tab.

---

## Reference

```
../SD-3502/                    the index, card actions and the create/edit modal
../SD-3504/                    per-client access to published benches
../../../prototype/index.html  → Internal Dashboard → V Benches → Technology Capacity Catalogue → View V Bench
```

Repository: https://github.com/luigimf/vbsuite
