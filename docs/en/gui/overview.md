# GUI Overview

Qt Widgets rendered on a `GuiCanvas` overlay: every widget is a real `QWidget` subclass with anchor layout, Fusion style and JSON serialization.

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `GuiCanvas` | `core/gui/canvasp/canvas.py` | Root `Panel` + child widgets, `add_widget`, `remove_widget`, `clear`, `serialize`/`deserialize`, `save_to_file`/`load_to_file` |
| `GuiApi` | `core/gui/api.py` | Helpers `panel`, `label`, `button`, `slider`, `input`, `image`, `toggle`, `progress`, `dropdown`, `scroll`, `mdi_area`, `clear`, `save`, `load` |
| `GuiCanvasSystem` | `core/gui/system.py` | Syncs entity GUI components to a canvas: `sync_entity`, `rebuild_from_scene`, `sync_all` |
| `WIDGET_REGISTRY` | `core/gui/wid/__init__.py` | `type` string to class map used by canvas deserialization |
| Anchors 0–11 | `core/gui/wid/base.py` | `ANCHOR_TOP_LEFT` … `ANCHOR_STRETCH_HEIGHT`, applied by `update_anchor(canvas_w, canvas_h)` |

## Usage in Editor

1. Add a GUI component (`ButtonComponent`, `LabelComponent`, etc.) to an entity.
2. Set `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag` in the Inspector.
3. `GuiCanvasSystem.rebuild_from_scene` creates the widgets; `sync_all` updates them per frame.
4. Canvas edit mode supports drag, snap-to-grid (`grid_size` 8), zoom and multi-select.

## Usage in Code

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
btn = gui.button(x=10, y=10, w=120, h=40, text="Play")
btn.clicked.connect(lambda: print("clicked"))
lbl = gui.label(x=10, y=60, w=200, h=30, text="Score: 0")
gui.save("ui/main.json")
gui.load("ui/main.json")
```

## Related

- Canvas, Controls, Containers, Layout and Events
- `core/components/gui/` entity components

## Verified against

- `core/gui/canvasp/canvas.py`, `core/gui/api.py`, `core/gui/system.py`
- `core/gui/wid/__init__.py`, `core/gui/wid/base.py`
