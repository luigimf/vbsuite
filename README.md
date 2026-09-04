# Master VB Suite

Specs and prototype for the **Master VB Suite** epic — a dedicated, co-branded demo environment
for partners such as Exertis, allowing them to demonstrate representative VB Suites independently,
and used by Castillians for our own demos.

Repository: https://github.com/luigimf/vbsuite

## Prototype

`prototype/index.html` is a self-contained bundle of the *Master VB Suite* canvas. Open it in a
browser — no build step, no server. The dark **Internal Dashboard / Manager Dashboard** toggle in
the header exists **only** so one file can demonstrate both sides; it is a prototype navigation
device and is **not** part of any story.

| Route in the prototype | Story |
|---|---|
| Internal Dashboard → V Benches → **VB Suite** tab | [SD-3502](https://castille-labs.atlassian.net/browse/SD-3502) |
| Internal Dashboard → V Benches → VB Suite → **View V Bench** | [SD-3503](https://castille-labs.atlassian.net/browse/SD-3503) |
| *(not in the prototype — Figma only)* Managers & Clients → Clients → Manage Client modal | [SD-3504](https://castille-labs.atlassian.net/browse/SD-3504) |
| *(not in the prototype — Figma only)* Manager profile drop-down → Castillians® Virtual Bench Suite | [SD-3505](https://castille-labs.atlassian.net/browse/SD-3505) |
| Manager Dashboard → **Virtual Bench Suite** (`/virtual-bench-suite`) | [SD-3506](https://castille-labs.atlassian.net/browse/SD-3506) |

Epic: [SD-3501 — Master VB Suite](https://castille-labs.atlassian.net/browse/SD-3501)

## Stories — Internal Dashboard

| Folder | Covers |
|---|---|
| `specs/internal-dashboard/SD-3502/` | VB Suite tab — master bench index, create/edit, status lifecycle, publish sync |
| `specs/internal-dashboard/SD-3503/` | VB Suite bench page — content sections, hours explorer, rate, engineer lists |
| `specs/internal-dashboard/SD-3504/` | Manage Client modal — Cobranded Client field move + VB Suite Access box |

All stories are live in Jira under epic **SD-3501**, Priority **High**, Assignee **Patrick**, Label **P2**.


## Reading `BE-nn`

Stories cite platform rules as **BE-01 … BE-30** — numbered sections in the main platform
`specs/ENGINEERING-BRIEF.md`, not the `BE.md` files in these folders.
