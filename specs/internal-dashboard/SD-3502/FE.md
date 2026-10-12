# FE — Technology Capacity Catalogue tab (master bench index)

> **Terminology (12 Oct 2026):** the display term **Technology Capacity Catalogue** replaces "VB Suite" / "Virtual Bench Suite" in all UI copy. Internal identifiers (`vb_suite_*` tables, `entitlements.virtualBenchSuite`) are **unchanged**. The page route is now **`/technology-capacity-catalogue`** (was `/virtual-bench-suite`), and the manager API paths follow it.

Angular handoff for **SD-3502**. A new **Technology Capacity Catalogue** tab on the Internal **V Benches** page: the
master template where our team builds the benches partners and clients demo.

> ### ⚠ The prototype's menubar is not part of this work
>
> The dark **Internal Dashboard / Manager Dashboard** toggle in the prototype's header exists
> **only** so one file can demonstrate both dashboards. It is a prototype navigation device and
> **must not be built.**
>
> **Keep the platform's existing menubar as it is.**

---

## Where it sits

- Fourth tab on **V Benches**, in this order: **Manage V Benches · Subscriptions · Mark-Ups &
  Discounts · Technology Capacity Catalogue**. Appended — the three existing tabs are untouched.
- Tab row: 10px gap, wraps; below 900px it scrolls horizontally with `white-space: nowrap` rather
  than wrapping mid-label.
- Tab: Bricolage **23px**, weight **700** active / **500** idle, `var(--ink-900)` active /
  `#9A9A9A` idle, active gets a white card (`--radius-md`, 10px 18px padding).
- The page title (**V Benches**) and the **Generate Reports** control are shared page furniture and
  do not change per tab.

## Components

| Component | Responsibility |
|---|---|
| `VbSuiteTabComponent` | Tab body: header, search, status filters, grid, empty state |
| `VbSuiteCardComponent` | One master bench card |
| `VbSuiteBenchModalComponent` | Create / Edit Details modal |
| `VendorLogoUploadComponent` | Vendor logo slot — shared with the bench page (SD-3503) |

## Header

- Title **Master Technology Capacity Catalogue**, Bricolage 26px/700.
- Intro, body 14px `var(--gray-700)`, max 620px: "Our official Technology Capacity Catalogue, built by our
  team. Published ones will be shared with clients and their assigned managers, set up via the
  Manage Clients page."
- **Create a Virtual Bench** — solid secondary button, 210×45, top-right, wraps under the copy on
  narrow widths.

## Search and status filters

One row, wrapping: search field `flex: 0 1 300px`, filter chips right.

- **Search benches** — 45px, 2px `var(--border)`, magnifier glyph, matches **bench name and core
  skills**, case-insensitive, live on every keystroke.
- **Status chips**: **All · Draft · Published · Archived**. Single-select, default **All**.
- Search AND status compose. Changing either resets the grid to the top.
- Counts are not shown on the chips.

## Grid

- `grid-template-columns: 1fr 1fr`, 22px gap. Single column below 1024px. **Wraps, never scrolls
  horizontally.**
- Card: white, **3px solid `#FFECD2`**, 8px radius, 30px padding, flex column, 20px gap. Card
  padding drops to 22px below 720px and the bench name to 26px.

Card contents, top to bottom:

| Element | Treatment |
|---|---|
| Vendor lockup | 25px logo tile (rounded 4px) + vendor name, Bricolage 17px/700, uppercase, `0.05em`, `#C29A65`, ellipsis |
| Status control | Native `select` — Draft / Published / Archived — tinted per status, right of the vendor lockup |
| Type tag | **Global** / **Data Regulated**, icon + label, in the type's own colour treatment |
| Bench name | Bricolage 34px/700, 112% line-height, `-0.01em` |
| Core skills | Chips: 1px `var(--gray-150)`, `var(--gray-50)` fill, 11px body |
| Engineers | Overlapping 28px avatars, each with the vetted badge, + count label 13px `#787878` |
| Divider | 1px `var(--gray-75)` |
| What it delivers | Uppercase 15px/700 heading + 4px bullet list, body 15px/145% |
| Outcome | Inset box `#FCFFFD`, 2px `#B9FFD1`, 8px radius, 10px padding; each line prefixed with `check-circle-green.svg` (19px) |
| Actions | **View V Bench** (opens SD-3503) and **Edit Details** (opens the modal), outline secondary, 45px, pinned to the card bottom with `margin-top: auto` |

**Status tints** — used by the card select and the bench-page Select alike:

| Status | Fill | Border | Text | Hover |
|---|---|---|---|---|
| Draft | `#FEF6E7` | `#F0D9A8` | `#7A5A12` | `#FBEDD2` |
| Published | `#EDFBF4` | `#9BE3C2` | `#0A7D56` | `#DFF6EA` |
| Archived | `#F8F8F8` | `#E5E5E5` | `#787878` | `#F2F2F2` |

**Empty state:** Bricolage 20px/700 title + body 14px, max 460px, no card, no illustration. The
copy distinguishes "nothing created yet" from "nothing matches this search or filter".

## Create / Edit modal

- Overlay `rgba(20,19,19,0.55)`, panel max **880px**, 12px radius, 36px 40px 40px padding, scrolls
  with the page on short viewports. Padding drops to 24px 20px 28px on mobile.
- Title **Create a V Bench for the Suite** / **Edit Virtual Bench**; CTA **Create Virtual Bench** /
  **Save changes**; secondary **Cancel**; close glyph top-right.
- Body copy differs per mode; the edit copy states that clients it has been shared with see the
  updated content on their manager dashboard.

| Field | Control | Required |
|---|---|---|
| Bench Name | Input, placeholder "e.g. Microsoft 365 Productivity" | **Yes** |
| Bench Type | Select — Global / Data Regulated | **Yes** |
| Vendor | Input, placeholder "e.g. Microsoft" | No |
| Vendor Logo | Upload slot, min-height 85px, 7px radius, contain fit, prefers SVG | No |
| Core skills | Chip field — type to search the skills library, Enter or click a suggestion to add, chips removable, 50px min height | **≥ 1** |
| What It Delivers | Repeating line inputs, **Add another** | **≥ 1** |
| Outcome | Repeating line inputs, **Add another** | No |

- **Capacity is not asked for here.** It is explored with the slider on the bench page (SD-3503).
- Validation runs on submit: missing fields go red and one inline error names them all —
  "Add a bench name, a bench type, at least one core skill and at least one 'what it delivers'
  bullet to save this bench." Blank repeating lines are dropped, not treated as content.
- A new bench is created as **Draft**.
- On mobile the vendor logo row stacks and the slot takes the full width.

## States

- **Loading:** the grid renders two skeleton cards; the header and controls render immediately.
- **Saving:** the modal CTA disables until the write resolves; the modal closes only on success.
- **Error:** a failed save keeps the modal open with the inline error style above.

---

## Reference

```
../SD-3503/                    the bench page View V Bench opens
../SD-3504/                    where a client is given access to published benches
../../../prototype/index.html  → Internal Dashboard → V Benches → Technology Capacity Catalogue
```

Repository: https://github.com/luigimf/vbsuite
