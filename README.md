# Master Technology Capacity Catalogue

Specs and prototype for the **Master Technology Capacity Catalogue** epic — a dedicated, co-branded demo environment
for partners such as Exertis, allowing them to demonstrate representative Technology Capacity Catalogues independently,
and used by Castillians for our own demos.

Repository: https://github.com/luigimf/vbsuite

## Prototype

`prototype/index.html` is a self-contained bundle of the *Master Technology Capacity Catalogue* canvas. Open it in a
browser — no build step, no server. The dark **Internal Dashboard / Manager Dashboard** toggle in
the header exists **only** so one file can demonstrate both sides; it is a prototype navigation
device and is **not** part of any story.

| Route in the prototype | Story |
|---|---|
| Internal Dashboard → V Benches → **Technology Capacity Catalogue** tab | [SD-3502](https://castille-labs.atlassian.net/browse/SD-3502) |
| Internal Dashboard → V Benches → Technology Capacity Catalogue → **View V Bench** | [SD-3503](https://castille-labs.atlassian.net/browse/SD-3503) |
| *(not in the prototype — Figma only)* Managers & Clients → Clients → Manage Client modal | [SD-3504](https://castille-labs.atlassian.net/browse/SD-3504) |
| *(not in the prototype — Figma only)* Manager profile drop-down → Castillians® Capacity Catalogue | [SD-3505](https://castille-labs.atlassian.net/browse/SD-3505) |
| Manager Dashboard → **Castillians® Capacity Catalogue** (`/technology-capacity-catalogue`) | [SD-3506](https://castille-labs.atlassian.net/browse/SD-3506) |

Epic: [SD-3501 — Master Technology Capacity Catalogue](https://castille-labs.atlassian.net/browse/SD-3501)

## Stories — Internal Dashboard

| Folder | Covers |
|---|---|
| `specs/internal-dashboard/SD-3502/` | Technology Capacity Catalogue tab — master bench index, create/edit, status lifecycle, publish sync |
| `specs/internal-dashboard/SD-3503/` | Technology Capacity Catalogue bench page — content sections, hours explorer, rate, engineer lists |
| `specs/internal-dashboard/SD-3504/` | Manage Client modal — Cobranded Client field move + Technology Capacity Catalogue Access box |

All stories are live in Jira under epic **SD-3501**, Priority **High**, Assignee **Patrick**, Label **P2**.


## Reading `BE-nn`

Stories cite platform rules as **BE-01 … BE-30** — numbered sections in the main platform
`specs/ENGINEERING-BRIEF.md`, not the `BE.md` files in these folders.
