# GUI Scripting

Driving UI from gameplay code: buttons, labels, canvas, and the `GuiApi` builders. Component names for lookup: `"ButtonComponent"`, `"LabelComponent"`, etc.

## Widget components

Every widget component shares the base fields (all serialized, all Inspector-editable):

| Field | Default | Notes |
|---|---|---|
| `widget_text` | `"Button"` / `"Label"` / … | Synced to the live Qt widget via `setText`/`text()`. |
| `widget_width` / `widget_height` | per-type (e.g. 120×36 button, 100×30 label) | Logical size, zoom-scaled to screen. |
| `_anchor` | `0` | Anchor preset id. |
| `_visible` | `True` | Visibility flag. |
| `_bg_color` / `_text_color` / `_border_color` | `None` | RGB lists, stylesheet-driven. |
| `_font_size` / `_font_bold` / `_opacity` | `14.0` / `False` / `1.0` | Appearance. |

Live-widget access:

| Member | Behavior |
|---|---|
| `comp._widget_ref` | The live `QWidget` (e.g. `Button(QPushButton)`), or `None` before creation/destroyed after. |
| `comp.sync_to_widget()` | Pushes component state (geometry, text, style) to the live widget. |
| `comp.update_from_widget()` | Pulls geometry/text back (editor drag path). |
| `Button.clicked` | Native Qt `QPushButton.clicked` signal — `connect` your slot. Also `textChanged`/`valueChanged`/`toggled` on other widgets auto-sync edits back. |

Widget position comes from the entity's `Transform.local_position` (x/y); size from `widget_width`/`widget_height`.

## Canvas and GuiApi

| Signature | Behavior |
|---|---|
| `GuiCanvas.add_widget(widget, parent_widget=None) -> QWidget` | Parents under `parent_widget` or the canvas root, shows it, fixes anchor/z-order. |
| `GuiCanvas.remove_widget(widget)` / `clear()` / `get_all_widgets()` | Removal, full clear, root-level listing. |
| `GuiCanvas.save_to_file(path)` / `load_from_file(path)` | JSON round-trip of the whole widget tree. Signals: `widget_selected`, `widget_changed`, `scene_modified`, `copy_requested`, `paste_requested`, `zoom_changed`. |
| `GuiApi.get(name="default") -> GuiApi` | Named canvas registry. |
| `api.label/button/panel/slider/input/image/toggle/progress/dropdown/scroll/mdi_area(...)` | Builder + auto-`add_widget`, returns the widget. |
| `api.clear()` / `api.save(path)` / `api.load(path)` | Canvas-level clear and file round-trip. |

## Counter-HUD example (full, runnable)

A label showing the score plus a button that increments it — Qt signals wired from a behavior script:

```python
class CounterHud:
    start_score: int = 0

    def on_start(self):
        scene = self._entity._scene
        self.score = int(self.start_score)
        self.label = scene.get_entity_by_name("ScoreLabel")
        btn_entity = scene.get_entity_by_name("ScoreButton")
        self._refresh()
        if btn_entity is not None:
            btn = btn_entity.get_component_by_name("ButtonComponent")
            w = btn._widget_ref if btn is not None else None
            if w is not None:
                try:
                    w.clicked.disconnect()
                except Exception:
                    pass
                w.clicked.connect(self._on_click)

    def _on_click(self):
        self.score += 1
        self._refresh()

    def _refresh(self):
        if self.label is None:
            return
        comp = self.label.get_component_by_name("LabelComponent")
        if comp is None:
            return
        comp.widget_text = f"Score: {self.score}"
        comp.sync_to_widget()

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.R):
            self.score = 0
            self._refresh()
```

Inspector fields: `start_score` (int). Setup: two entities named `ScoreLabel` (LabelComponent) and `ScoreButton` (ButtonComponent) anywhere in the scene.

Building UI purely from code with `GuiApi`:

```python
class MenuBuilder:
    def on_start(self):
        from core.gui.api import GuiApi
        api = GuiApi.get("default")
        if api is None:
            Logger.warning("MenuBuilder: no default GuiApi")
            return
        title = api.label(x=300, y=40, w=200, h=40, text="PAUSED")
        btn = api.button(x=300, y=120, w=200, h=48, text="Resume")
        btn.clicked.connect(self._on_resume)
        self._built = [title, btn]

    def _on_resume(self):
        Logger.info("Resume pressed")

    def on_destroy(self):
        from core.gui.api import GuiApi
        api = GuiApi.get("default")
        if api is not None:
            api.clear()
```

Saving a layout to disk and restoring it:

```python
api.save("assets/ui/hud.json")   # GuiCanvas.save_to_file under the hood
api.load("assets/ui/hud.json")   # GuiCanvas.load_from_file under the hood
```

## Related

- `entity-api.md` — finding widget entities by name and caching components.
- `input-reference.md` — `KeyCode.R`, mouse codes used in UI scripts.
- `templates.md` — the Counter HUD as a starter template.

## Verified against

- `core/components/gui/controls/button_component.py:12-23` (`ButtonComponent`, `widget_text="Button"`), `label_component.py:12-39` (`LabelComponent`, `_halign`).
- `core/components/gui/layout/basewidget/component.py:12-119` (shared fields + serialize), `runtime.py:87-140` (widget creation + `setText(widget_text)`), `:142-208` (sync paths), `:230-276` (pull-back).
- `core/gui/wid/buttons.py` (`Button(QPushButton)` ⇒ native `clicked` signal).
- `core/gui/api.py:24-111` (`GuiApi` builders, `save`/`load`), `core/gui/canvasp/canvas.py:28-33` (signals), `:179-206` (add/remove/clear), `:279-285` (file round-trip).
