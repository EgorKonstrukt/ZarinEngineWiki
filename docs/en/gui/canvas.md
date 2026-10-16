# GuiCanvas

`GuiCanvas` (`QWidget`) hosts all widgets as children of a stretch-all root `Panel`, with edit-mode drag, anchors, z-order and JSON save/load.

## Properties / API

| Member | Source | Meaning |
|---|---|---|
| `root: Panel` | `core/gui/canvasp/canvas.py` | Stretch-all background panel, parent of all widgets |
| `screen_width / screen_height` | `canvas.py` | Virtual resolution, min 320x240, drives `_update_root_geometry` |
| `zoom` | `canvas.py` | 0.1–10.0, emits `zoom_changed` |
| `edit_mode` | `canvas.py` | When on, children disabled for drag/resize |
| `snap_to_grid / grid_size / auto_align` | `canvas.py` | Grid snap (default 8) and alignment guides |
| `add_widget / remove_widget / clear / get_all_widgets` | `canvas.py` | Child management |
| `serialize / deserialize / save_to_file / load_from_file` | `canvas.py` | JSON with `canvas_w`, `canvas_h`, `children` (`type`, `id`, `children`) |
| `NATIVE_WIDGET_TYPES` | `canvas.py` | 30 names: Button, Label, Slider, TextInput, Toggle, ProgressBar, Dropdown, ScrollPanel, Image, Panel, RadioButton, ListWidget, TableWidget, TreeWidget, TabWidget, GroupBox, SpinBox, DoubleSpinBox, TextEdit, Dial, HtmlView, Splitter, StackedWidget, ToolBox, Calendar, LCDNumber, PlainText, ScrollBar, ToolButton, FontCombo (plus MdiArea/MdiSubWindow/Chart handled via `WIDGET_REGISTRY`: 34 keys — 33 types with alias `Chart` → `ChartPlotter`) |
| Signals | `canvas.py` | `widget_selected`, `widget_changed`, `scene_modified`, `copy_requested`, `paste_requested`, `zoom_changed` |

## Usage in Editor

1. Canvas viewport shows the widget tree; select, drag, resize with handles.
2. Set canvas `screen_width`/`screen_height` to preview resolutions; anchors re-apply via `update_anchor`.
3. `save_to_file` stores layout JSON; `load_from_file` restores it with `WIDGET_REGISTRY` types.

## Usage in Code

```python
from core.gui.canvasp import GuiCanvas
from core.gui.wid import Button

canvas = GuiCanvas()
btn = Button(x=20, y=20, w=120, h=40, text="OK")
canvas.add_widget(btn)
canvas.save_to_file("ui/hud.json")
canvas.load_from_file("ui/hud.json")
```

## Related

- GUI Overview, Controls, Containers, Layout and Events

## Verified against

- `core/gui/canvasp/canvas.py`, `core/gui/wid/__init__.py`
