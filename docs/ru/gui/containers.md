# Контейнеры

Только контейнеры из `core/gui/wid/containers.py`. Каждая позиция — одна строка.

## Свойства / API

| Виджет | База |
|---|---|
| `Panel` — простая рамка-контейнер. | `QFrame` |
| `GroupBox` — рамка с заголовком `title`. | `QGroupBox` |
| `TabWidget` — вкладки, `tab_count`. | `QTabWidget` |
| `StackedWidget` — стек страниц по индексу. | `QStackedWidget` |
| `Splitter` — раздвижные панели. | `QSplitter` |
| `ScrollPanel` — скролл с `scroll_content`. | `QScrollArea` |
| `MdiSubWindow` — дочернее MDI-окно с `title`. | `QMdiSubWindow` |
| `MdiArea` — MDI-хост: `add_sub_window`, `tile_windows`, `cascade_windows`, `close_all`. | `QMdiArea` |
| `ToolBox` — стек инструментальных страниц. | `QToolBox` |

Общие поля: `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag`, `serialize()` / `deserialize()`.

## Использование в редакторе

1. Добавьте парный компонент (`PanelComponent`, `TabWidgetComponent`, `MdiAreaComponent`, …).
2. Дочерние сущности вкладывайте под контейнер; `GuiCanvasSystem` перепарентит Qt-виджеты.
3. Для скролла кладите контент в `ScrollPanel.scroll_content`.

## Использование в коде

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
panel = gui.panel(x=0, y=0, w=300, h=200)
gui.button(x=10, y=10, w=120, h=40, text="Внутри", parent=panel)
area = gui.mdi_area(x=0, y=220, w=400, h=300)
```

## Связанные

- Обзор GUI, Канвас, Контролы, Раскладка и события

## Проверено по

- `core/gui/wid/containers.py`, `core/gui/api.py`, `core/gui/system.py`
