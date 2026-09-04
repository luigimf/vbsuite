# FE — Manager Virtual Bench Suite page (view only)

Angular handoff for **SD-3506**. The page at **/virtual-bench-suite**: the client-facing mirror of
the Master VB Suite, **view only**, synced in real time from the internal tool that sets the benches
up.

> ### ⚠ The prototype's menubar is not part of this work
>
> The dark **Internal Dashboard / Manager Dashboard** toggle in the prototype's header exists
> **only** so one file can demonstrate both dashboards. It is a prototype navigation device and
> **must not be built.** The menubar and the entry point are SD-3505.

---

## What "view only" means here

Everything on this page is read from the master bench. **The only interactive controls are the type
filter, the capacity slider and the enquiry form.** There is no create, no edit, no status control,
no engineer search, no drag, no delete.

## Header card

- Title **Virtual Bench Suite**, Bricolage 42px/700 (30px below 720px).
- Intro, body 17px `var(--gray-700)`, max 700px: "Ready-to-go Virtual Benches managed by
  Castillians. These operate as on-demand, continuous capability layers backed by verified
  engineering capacity on subscription, aligned with leading vendor ecosystems."
- **Type filter**, right, `flex: 0 1 240px`: a 50px trigger reading **All Virtual Benches** by
  default, or the selected type; chevron rotates 180° over 300ms.
- Panel: 380px, 12px radius, 2px `var(--border)`, `0 4px 41px rgba(0,0,0,.1)`, right-aligned under
  the trigger; **flips to left-aligned below 1024px** so it cannot run off the edge. Three rows,
  each an icon tile + title + description, 1px `var(--gray-75)` between:

| Row | Description |
|---|---|
| **All Virtual Benches** | Every bench in the Castillians suite, both types |
| **Global** | The Global bench type, in its own colour treatment |
| **Data Regulated** | The Data Regulated bench type, in its own colour treatment |

- The selected row holds a `#F2F2F2` background; hover is `#F8F8F8`.
- There is **no search field and no status filter** on this side — status is not a manager concept.

## Bench index

- Same card geometry as the internal grid: two columns, 22px gap, one column below 1024px, 3px
  `#FFECD2` border, 8px radius, 30px padding.
- Card carries the vendor lockup, the **type tag top-right**, bench name, core skill chips, engineer
  avatars with vetted badges and a count, *What it delivers*, and the *Outcome* box.
- **No status control** and **no Edit Details** — the only action is **View V Bench**.
- Empty state: Bricolage 20px/700 title + body, distinguishing "nothing available yet" from
  "nothing matches this type filter".

## Bench detail

- **Back to Virtual Bench Suite** — arrow + label, top-left.
- **Enquire about this V Bench** — top-right, 35px, mail glyph, `#F8F8F8` fill, 2px `#E5E5E5`.
- Head card: vendor lockup, type tag, bench name (42px). **No status Select, no Delete.**
- **Skills Mix**, **Skills Matrix**, **What It Delivers**, **Outcome** — identical presentation to
  the internal page, **without** the *Edit content* buttons.
- **Monthly Engineering Hours** — the slider **is** interactive here: 40–1000, step 10. It is the
  manager's own exploration; moving it recalculates **Subscription Rate** on their side only.
- The rate heading reads **Subscription Rate ({client currency})** — taken from the manager's client
  account, never a hard-coded EUR. The internal-only currency tooltip is not shown.
- **No Configured Rate control.**

## Engineers — profile cards

The manager sees full profile cards rather than the internal compact rows, one per engineer, in the
order set internally.

- Tabs **Available (n)** / **Recommended (n)** — same treatment, **no drag, no search, no remove**.
- Card: 1px `var(--border)`, 10px radius, 40px 24px padding, and:
  - **Castillian badge** top-right (42×60) with tooltip: vetted on skills and experience, passed KYC
    and preliminary interviews, ready to engage.
  - **Work from anywhere** and **AI ready** labels where they apply, each with its tooltip. These are
    **engineer-level** badges and are unrelated to the bench's Global / Data Regulated type.
  - 56px avatar with vetted badge, name (26px/700), rating bars + score + review count.
  - Chips: availability (with a `#10b77f` dot), seniority, country with flag, and time-zone group
    (code in 600 + cities).
  - **Top technical skills** — chips each with a 5-bar rating beneath, plus a "+n more" count.
  - **Top interpersonal skills** — labels each with a 5-bar rating beneath, plus a "+n more" count.
  - **View Profile** — solid secondary, 150×45, opens the engineer's profile in a new tab.
- **Engineer rates are never shown on this side.** The manager sees the subscription rate only.
- Empty: "No engineers in this list yet."

## Enquire about this V Bench

- Modal, max 620px. Copy states nothing is committed and that our team comes back with a plan, a
  rate and the engineers being put forward.
- **Capacity (hours per month)** — select: 40 / 80 / 160 / 500 / 1,000 hrs per month, defaulting to
  the bench's current capacity. Required.
- **Preferred start** — date input. Required.
- **Anything else? (optional)** — textarea, placeholder "Scope, skills you care about, how you would
  want to scale".
- An info strip: "We typically respond within 24 hours during business days".
- Validation on send: missing fields produce one inline error naming them and outline the offending
  field in `var(--danger-500)`.
- Success state replaces the form: the enquiry is with Human Capital, a copy has been emailed to the
  manager, and **nothing is committed or charged**. Single **Done** button.

## States

- **Loading:** header renders immediately; the grid shows two skeleton cards.
- **Real-time change while open:** a bench unpublished, archived or removed from this client's
  selection **disappears** from the index; if it is the bench currently open, the page returns to
  the index with a plain "this bench is no longer available" line. New and edited benches appear or
  update in place — no reload, no toast storm.

---

## Reference

```
../SD-3505/                          the menubar option and the route guard
../../internal-dashboard/SD-3502/    the master benches and their statuses
../../internal-dashboard/SD-3503/    the content, rate and engineer lists being mirrored
../../internal-dashboard/SD-3504/    which managers and which benches this client sees
../../../prototype/index.html        → Manager Dashboard → Virtual Bench Suite
```

Repository: https://github.com/luigimf/vbsuite
