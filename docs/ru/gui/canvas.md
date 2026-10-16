# Канвас GuiCanvas

`GuiCanvas` (`QWidget`) держит все виджеты детьми корневой `Panel` на stretch-all, с drag в edit-mode, якорями, z-порядком и сохранением в JSON.

## Свойства / API

| Член | Источник | Смысл |
|---|---|---|
| `root: Panel` | `core/gui/canvasp/canvas.py` | Фоновая панель stretch-all, родитель всех виджетов |
| `screen_width / screen_height` | `canvas.py` | Виртуальное разрешение, мин. 320x240 |
| `zoom` | `canvas.py` | 0.1–10.0, сигнал `zoom_changed` |
| `edit_mode` | `canvas.py` | Вкл — дети отключены для drag/resize |
| `snap_to_grid / grid_size / auto_align` | `canvas.py` | Снап к сетке (по умолч. 8) и гайды |
| `add_widget / remove_widget / clear / get_all_widgets` | `canvas.py` | Управление детьми |
| `serialize / deserialize / save_to_file / load_from_file` | `canvas.py` | JSON `canvas_w`, `canvas_h`, `children` (`type`, `id`, `children`) |
| `NATIVE_WIDGET_TYPES` | `canvas.py` | 30 имён: Button, Label, Slider, TextInput, Toggle, ProgressBar, Dropdown, ScrollPanel, Image, Panel, RadioButton, ListWidget, TableWidget, TreeWidget, TabWidget, GroupBox, SpinBox, DoubleSpinBox, TextEdit, Dial, HtmlView, Splitter, StackedWidget, ToolBox, Calendar, LCDNumber, PlainText, ScrollBar, ToolButton, FontCombo (плюс MdiArea/MdiSubWindow/Chart через `WIDGET_REGISTRY`: 34 ключа — 33 типа с алиасом `Chart` → `ChartPlotter`) |
| Сигналы | `canvas.py` | `widget_selected`, `widget_changed`, `scene_modified`, `copy_requested`, `paste_requested`, `zoom_changed` |

## Использование в редакторе

1. Вьюпорт канваса показывает дерево виджетов; выбор, drag, resize хэндлами.
2. Задайте `screen_width`/`screen_height` для превью разрешений; якоря пересчитываются через `update_anchor`.
3. `save_to_file` хранит JSON раскладки; `load_from_file` восстанавливает по `WIDGET_REGISTRY`.

## Использование в коде

```python
from core.gui.canvasp import GuiCanvas
from core.gui.wid import Button

canvas = GuiCanvas()
btn = Button(x=20, y=20, w=120, h=40, text="OK")
canvas.add_widget(btn)
canvas.save_to_file("ui/hud.json")
canvas.load_from_file("ui/hud.json")
```

## Связанные

- Обзор GUI, Контролы, Контейнеры, Раскладка и события

## Проверено по

- `core/gui/canvasp/canvas.py`, `core/gui/wid/__init__.py`
