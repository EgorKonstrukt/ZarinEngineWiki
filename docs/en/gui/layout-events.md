# Layout, Anchors, Styles, Events

Anchors position widgets on canvas resize; layouts arrange children; Fusion style skins them; events are Qt signals plus `GuiEvent` helpers.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| Anchors 0–11 | `core/gui/wid/base.py` | `ANCHOR_TOP_LEFT` 0 … `ANCHOR_STRETCH_HEIGHT` 11; `_update_anchor` computes `x,y,w,h` from `canvas_w/h` |
| `AnchorPresetSelector` | `core/gui/wid/base.py` | 3x4 picker widget, `anchor_changed` signal |
| `HorizontalLayoutComponent` / `VerticalLayoutComponent` / `GridLayoutComponent` | `core/components/gui/layout/horizontal_layout_component.py` | `QHBoxLayout` / `QVBoxLayout` / `QGridLayout` sync via `_sync_layout(canvas)` |
| `LayoutElementComponent` | `core/components/gui/layout/layout_element_component.py` | Per-child layout element, `_sync_layout_element(widget)` |
| `GuiWidgetComponent` | `core/components/gui/layout/gui_widget_component.py` | Base: `_widget_ref`, `_create_widget(canvas)`, `sync_to_widget()` |
| `TooltipComponent` | `core/components/gui/controls/tooltip_component.py` | Attaches `text` (+ `_duration` sec, `_rich_text`) to the sibling widget via `setToolTip` / `setToolTipDuration`; single-instance (`_allow_multiple = False`) |
| `GuiEvent` / `ClickEvent` / `ValueChangeEvent` / `HoverEvent` / `FocusEvent` | `core/gui/events.py` | `connect`, `disconnect`, `emit(sender, **kwargs)`, `once` |
| `apply_fusion_style` / `get_widget_base_style` | `core/gui/wid/base.py` | Object-name styles (`GuiButton`, `GuiLabel`, …) |
| Canvas overlay/events mixins | `core/gui/canvasp/overlay.py`, `core/gui/canvasp/events.py` | Grid paint, drag/resize, selection box, pan/zoom |

## Usage in Editor

1. Set `anchor` on each widget (top-left default; stretch-all for background panels).
2. Add `HorizontalLayoutComponent`, `VerticalLayoutComponent` or `GridLayoutComponent` to a container entity to auto-arrange children.
3. Tune `LayoutElementComponent` on children for stretch/fill behavior.
4. Pick style via Fusion stylesheet; object names (`GuiButton`, …) select rules.

## Usage in Code

```python
btn = gui.button(x=10, y=10, w=120, h=40, text="OK")
btn._anchor = 2  # ANCHOR_TOP_RIGHT
btn.update_anchor(canvas.width(), canvas.height())
btn.clicked.connect(lambda: print("ok"))
```

## Related

- GUI Overview, Canvas, Controls, Containers

## Verified against

- `core/gui/wid/base.py`, `core/gui/events.py`
- `core/gui/canvasp/events.py`, `core/gui/canvasp/overlay.py`
- `core/components/gui/layout/horizontal_layout_component.py`
