# FE — Manage Client modal: Cobranded field move + VB Suite Access

Angular handoff for **SD-3504**. Two changes to the **Manage Client** modal on the existing
Internal Dashboard, reached from **Managers & Clients → Clients**.

> ### ⚠ Not in the prototype
>
> This story is **not** represented in the Master VB Suite prototype. The UI is specified in Figma —
> the three frames linked in the Reference block are the source of truth for layout, spacing and
> every control state. This document specifies behaviour, not a new visual language.

---

## 1. Move **Cobranded Client** into **Cobranded Platform Set Up**

- The **Cobranded Client** field moves out of the **Client Details** box and into the
  **Cobranded Platform Set Up** box, as its **first** field.
- **The logic is unchanged**: same field, same Yes/No semantics, same downstream effects, same
  persisted value. This is a placement change only.
- The rest of **Client Details** closes the gap; no other field moves and none is re-ordered.
- **Cobranded Platform Set Up** keeps its existing behaviour for the fields already in it; the
  moved field simply joins them, and the box's existing show/hide logic now keys off the field in
  its own box.
- UI states: [Figma — Cobranded Platform Set Up](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-15999&t=aTJFhDRsi2oAclPH-11).

**Acceptance criteria**
- The field is absent from Client Details and present in Cobranded Platform Set Up.
- An existing client's saved value is displayed unchanged after the move — no migration, no reset,
  no default applied on first open.
- Validation, enable/disable behaviour and any dependent fields behave exactly as before.

## 2. New box — **VB Suite Access**

A new box in the same modal, giving a client's managers access to the benches our team publishes in
the **Master VB Suite** (SD-3502).

### Collapsed state

| Field | Control | Default |
|---|---|---|
| **VB Suite Access** | Yes / No | **No** |

- With **No**, the box shows only this field.
- With **Yes**, the box **expands** to reveal the two selection fields below.
- UI states: [Figma — VB Suite Access](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-16326&t=aTJFhDRsi2oAclPH-11).

### Expanded state

| Field | Control | Default |
|---|---|---|
| **Managers with access** | Multi-select of the managers **within this client organisation** | **All** |
| **Virtual Benches to be shown** | Multi-select of **Published** benches from the Master VB Suite | **All** |

- Both are multi-select pickers with a **select-all / deselect-all tick box** at the top of the
  list, per [Figma — picker states](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-16490&t=aTJFhDRsi2oAclPH-11).
- The select-all box reflects three states: all selected, none selected, and **indeterminate** when
  a subset is chosen.
- **Managers with access** lists only managers belonging to this client organisation — never
  managers from another client.
- **Virtual Benches to be shown** lists only benches whose status is **Published**. Draft and
  Archived benches are not offered.
- Each field shows a count summary when not all are selected (e.g. "3 of 8 selected"); when
  everything is selected it reads **All**.
- Setting **VB Suite Access** back to **No** collapses the box and **withholds access** from every
  manager in the organisation. The selections are retained so switching back to Yes restores them
  rather than resetting to All.

**Acceptance criteria**
- With access **No**, no manager in the organisation sees the Virtual Bench Suite page at all.
- With access **Yes** and both fields on **All**, every manager in the organisation sees every
  Published bench.
- Deselecting a manager removes their access without affecting any other manager.
- Deselecting a bench hides it from **all** managers in this organisation, whatever the manager
  selection says — the two fields compose as AND.
- Saving with access **Yes** and **zero** managers, or **zero** benches, is rejected with an inline
  error: an empty selection is almost certainly a mis-click, and "no access" is expressed by the
  Yes/No field.

### Defaults and new benches

- Both fields are left on **All** by default, and **All is a live rule, not a snapshot**: when our
  team creates and publishes a **new** bench in the Master VB Suite, it appears automatically for
  every client whose **Virtual Benches to be shown** is on All — with no action in this modal.
- The same applies to **Managers with access**: a manager added to the organisation later inherits
  access while the field is on All.
- Once a field has been narrowed to a subset, it is **explicit** — new benches and new managers are
  **not** added to it automatically. The count summary is what tells an operator they are on a
  subset.

---

## Reference

```
../SD-3502/                    the Master VB Suite the access is granted to
../SD-3503/                    what a manager sees once granted
```

Figma — [Cobranded Platform Set Up](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-15999&t=aTJFhDRsi2oAclPH-11) · [VB Suite Access](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-16326&t=aTJFhDRsi2oAclPH-11) ·
[picker states](https://www.figma.com/design/A4ZWwuOmoeUr7p72TYIiLV/castillians.com?node-id=12666-16490&t=aTJFhDRsi2oAclPH-11)

Repository: https://github.com/luigimf/vbsuite
