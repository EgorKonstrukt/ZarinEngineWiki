# Containers

Only container widgets that exist in `core/gui/wid/containers.py`. Each entry is one line.

## Properties / API

| Widget | Base |
|---|---|
| `Panel` — plain frame container. | `QFrame` |
| `GroupBox` — titled frame with `title`. | `QGroupBox` |
| `TabWidget` — tabbed pages, `tab_count`. | `QTabWidget` |
| `StackedWidget` — indexed page stack. | `QStackedWidget` |
| `Splitter` — resizable split panes. | `QSplitter` |
| `ScrollPanel` — scroll area with `scroll_content` widget. | `QScrollArea` |
| `MdiSubWindow` — single MDI child with `title`. | `QMdiSubWindow` |
| `MdiArea` — MDI host with `add_sub_window`, `tile_windows`, `cascade_windows`, `close_all`. | `QMdiArea` |
| `ToolBox` — stacked tool pages. | `QToolBox` |

Common fields: `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag`, `serialize()` / `deserialize()`.

## Usage in Editor

1. Add the matching component (`PanelComponent`, `TabWidgetComponent`, `MdiAreaComponent`, …) to an entity.
2. Nest child widget entities under the container entity; `GuiCanvasSystem` parents their Qt widgets.
3. Use `ScrollPanel.scroll_content` as the parent for scrollable content.

## Usage in Code

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
panel = gui.panel(x=0, y=0, w=300, h=200)
btn_holder = gui.button(x=10, y=10, w=120, h=40, text="Inside", parent=panel)
area = gui.mdi_area(x=0, y=220, w=400, h=300)
```

## Related

- GUI Overview, Canvas, Controls, Layout and Events

## Verified against

- `core/gui/wid/containers.py`, `core/gui/api.py`, `core/gui/system.py`
