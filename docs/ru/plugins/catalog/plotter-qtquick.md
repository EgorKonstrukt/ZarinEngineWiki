# Плоттер + QtQuick-миры

Два UI-плагина: док графиков свойств в реальном времени и QtQuick-виджеты в мировом пространстве 3D-сцены.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `PlotterPlugin` | `1.2.0` | `False` |
| `QtQuickWorldPlugin` | `1.0.0` | `True` |

`PlotterPlugin` объявлен в `plugins/plotter_plugin/__init__.py` (манифест `plugins/plotter_plugin/manifest.json`). `QtQuickWorldPlugin` объявлен как `QtQuickWorldPluginBase` в `plugins/qt_quick_plugin/plug/plugin.py` (манифест `plugins/qt_quick_plugin/manifest.json`, `provides: {}`).

## Файлы

- `plugins/plotter_plugin/panel.py` — `PlotterPanel(engine, plugin, persist=True)`: пикеры + `ChartWidget`, `set_active(bool)`.
- `plugins/plotter_plugin/plotter/` — `ChartWidget`, `chart/series.py` (`Series.plot(...)`), canvas-бэкенды, сайдбар, палитра.
- `plugins/qt_quick_plugin/plug/plugin.py` — аттач рантайма, рендер-хук, меню `QtQuick → Create …` (Button, Label, Slider, Field, Check, Bar, Switch, Dial, Combo, Spin, Panel, Image), `Refresh All`, `Diagnose`, кнопка тулбара `QuickUI`.
- `plugins/qt_quick_plugin/components/quick_view.py` — базовый компонент `QuickView`.
- `plugins/qt_quick_plugin/components/controls/` — `QuickButton`, `QuickLabel`, `QuickSlider`, … (`QUICK_ALL_TYPES`).
- `plugins/qt_quick_plugin/runtime/`, `gl/`, `interact/` — QML-рантайм, GL-рендерер, пикинг.

## API

Плоттер: док `"Plotter"` (область `left`, иконка `fa5s.chart-line`), меню `Tools → Open Plotter` / `Tools → New Plotter Window`, тулбар `New Plotter`. `PlotterPanel` опрашивает по `QTimer` (`refresh_interval` дефолт `100` мс, `history_limit` дефолт `2000` из конфига плагина): выберите X (время или свойство сущности) и Y (свойство через `EntityPropertyPicker`), нажмите `Track`, смотрите `ChartWidget(show_legend, show_toolbar)`; `Clear` сбрасывает серии.

Поля `QuickView`: `qml_text` / `qml_file` (`*.qml`), `width_px` / `height_px` (32–2048, дефолт 512), `size_x` / `size_y` в метрах (дефолт 1.0), `transparent` (дефолт True), `billboard` (`Off`/`Full`/`Y`), `interactive` (дефолт True), `opacity`, `render_order`, `double_sided`, `animated`, `tint`. `QuickButton` добавляет `text` (дефолт `"Button"`), `font_size` (дефолт 28), `checkable`/`checked`, readonly `click_count`, методы `add_on_clicked(fn)` / `remove_on_clicked(fn)` / `click()` / `get_value()`.

## Пример — график плюс мировая кнопка

```python
# Плоттер (редактор): выберите сущность, откройте док Plotter,
# Y-пикер -> Transform -> position.x, нажмите "Track", нажмите Play.
# График сэмплирует свойство каждые refresh_interval мс.

# QtQuick-кнопка в мире (скрипт на сущности с QuickButton):
def on_start(self):
    btn = self._entity.get_component_by_name("QuickButton")
    btn.text = "Launch"
    btn.add_on_clicked(self._on_launch)

def _on_launch(self, btn):
    Logger.info(f"Launched, clicks={btn.click_count}")
```

Путь через меню вместо кода: `QtQuick → Create Button` спавнит сущность `QuickButton`; `Text` / `Size X m` / `Billboard` правятся в Inspector.

## Проверено по коду

`plugins/plotter_plugin/__init__.py`, `plugins/plotter_plugin/manifest.json`, `plugins/plotter_plugin/panel.py`, `plugins/plotter_plugin/plotter/chart/series.py`, `plugins/qt_quick_plugin/__init__.py`, `plugins/qt_quick_plugin/manifest.json`, `plugins/qt_quick_plugin/plug/plugin.py`, `plugins/qt_quick_plugin/components/quick_view.py`, `plugins/qt_quick_plugin/components/controls/buttons.py`.
