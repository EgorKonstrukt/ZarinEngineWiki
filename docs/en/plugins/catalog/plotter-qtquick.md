# Plotter + QtQuick World UI

Two UI-side plugins: a real-time property chart dock, and world-space QtQuick widgets rendered into the 3D scene.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `PlotterPlugin` | `1.2.0` | `False` |
| `QtQuickWorldPlugin` | `1.0.0` | `True` |

`PlotterPlugin` is declared in `plugins/plotter_plugin/__init__.py` (manifest `plugins/plotter_plugin/manifest.json`). `QtQuickWorldPlugin` is declared as `QtQuickWorldPluginBase` in `plugins/qt_quick_plugin/plug/plugin.py` (manifest `plugins/qt_quick_plugin/manifest.json`, `provides: {}`).

## Files

- `plugins/plotter_plugin/panel.py` — `PlotterPanel(engine, plugin, persist=True)`: pickers + `ChartWidget`, `set_active(bool)`.
- `plugins/plotter_plugin/plotter/` — `ChartWidget`, `chart/series.py` (`Series.plot(...)`), canvas backends, sidebar, palette.
- `plugins/qt_quick_plugin/plug/plugin.py` — runtime attach, render hook, `QtQuick → Create …` menu (Button, Label, Slider, Field, Check, Bar, Switch, Dial, Combo, Spin, Panel, Image), `Refresh All`, `Diagnose`, `QuickUI` toolbar button.
- `plugins/qt_quick_plugin/components/quick_view.py` — `QuickView` base component.
- `plugins/qt_quick_plugin/components/controls/` — `QuickButton`, `QuickLabel`, `QuickSlider`, … (`QUICK_ALL_TYPES`).
- `plugins/qt_quick_plugin/runtime/`, `gl/`, `interact/` — QML runtime, GL renderer, picking.

## API

Plotter: dock `"Plotter"` (area `left`, icon `fa5s.chart-line`), menu `Tools → Open Plotter` / `Tools → New Plotter Window`, toolbar `New Plotter`. `PlotterPanel` polls on a `QTimer` (`refresh_interval` default `100` ms, `history_limit` default `2000` from plugin config): pick X (time or entity property) and Y (entity property via `EntityPropertyPicker`), press `Track`, watch `ChartWidget(show_legend, show_toolbar)`; `Clear` drops all series.

`QuickView` fields: `qml_text` / `qml_file` (`*.qml`), `width_px` / `height_px` (32–2048, default 512), `size_x` / `size_y` meters (default 1.0), `transparent` (default True), `billboard` (`Off`/`Full`/`Y`), `interactive` (default True), `opacity`, `render_order`, `double_sided`, `animated`, `tint`. `QuickButton` adds `text` (default `"Button"`), `font_size` (default 28), `checkable`/`checked`, readonly `click_count`, plus `add_on_clicked(fn)` / `remove_on_clicked(fn)` / `click()` / `get_value()`.

## Example — chart plus world-space button

```python
# Plotter (editor): select any entity, open the Plotter dock,
# Y-picker -> Transform -> position.x, press "Track", press Play.
# The chart samples the property every refresh_interval ms.

# QtQuick world button (script side, on an entity with QuickButton):
def on_start(self):
    btn = self._entity.get_component_by_name("QuickButton")
    btn.text = "Launch"
    btn.add_on_clicked(self._on_launch)

def _on_launch(self, btn):
    Logger.info(f"Launched, clicks={btn.click_count}")
```

Menu path instead of code: `QtQuick → Create Button` spawns a `QuickButton` entity; edit `Text` / `Size X m` / `Billboard` in the Inspector.

## Verified against

`plugins/plotter_plugin/__init__.py`, `plugins/plotter_plugin/manifest.json`, `plugins/plotter_plugin/panel.py`, `plugins/plotter_plugin/plotter/chart/series.py`, `plugins/qt_quick_plugin/__init__.py`, `plugins/qt_quick_plugin/manifest.json`, `plugins/qt_quick_plugin/plug/plugin.py`, `plugins/qt_quick_plugin/components/quick_view.py`, `plugins/qt_quick_plugin/components/controls/buttons.py`.
