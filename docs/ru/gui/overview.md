# Обзор GUI

Qt-виджеты на оверлее `GuiCanvas`: каждый виджет — настоящий подкласс `QWidget` с якорной раскладкой, стилем Fusion и JSON-сериализацией.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `GuiCanvas` | `core/gui/canvasp/canvas.py` | Корневая `Panel` + дети, `add_widget`, `remove_widget`, `clear`, `serialize`/`deserialize`, `save_to_file`/`load_from_file` |
| `GuiApi` | `core/gui/api.py` | Хелперы `panel`, `label`, `button`, `slider`, `input`, `image`, `toggle`, `progress`, `dropdown`, `scroll`, `mdi_area`, `clear`, `save`, `load` |
| `GuiCanvasSystem` | `core/gui/system.py` | Синхронизация GUI-компонентов сущностей с канвасом: `sync_entity`, `rebuild_from_scene`, `sync_all` |
| `WIDGET_REGISTRY` | `core/gui/wid/__init__.py` | Маппинг строки `type` в класс для десериализации канваса |
| Якоря 0–11 | `core/gui/wid/base.py` | `ANCHOR_TOP_LEFT` … `ANCHOR_STRETCH_HEIGHT`, применяются через `update_anchor(canvas_w, canvas_h)` |

## Использование в редакторе

1. Добавьте GUI-компонент (`ButtonComponent`, `LabelComponent` и т.д.) на сущность.
2. Задайте `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag` в Inspector.
3. `GuiCanvasSystem.rebuild_from_scene` создаёт виджеты; `sync_all` обновляет их каждый кадр.
4. Редактор канваса: drag, snap-to-grid (`grid_size` 8), zoom, мультивыделение.

## Использование в коде

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
btn = gui.button(x=10, y=10, w=120, h=40, text="Играть")
btn.clicked.connect(lambda: print("clicked"))
lbl = gui.label(x=10, y=60, w=200, h=30, text="Счёт: 0")
gui.save("ui/main.json")
gui.load("ui/main.json")
```

## Связанные

- Канвас, Контролы, Контейнеры, Раскладка и события

## Проверено по

- `core/gui/canvasp/canvas.py`, `core/gui/api.py`, `core/gui/system.py`
- `core/gui/wid/__init__.py`, `core/gui/wid/base.py`
