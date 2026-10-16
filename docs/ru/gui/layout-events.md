# Раскладка, якоря, стили, события

Якоря позиционируют виджеты при ресайзе канваса; раскладки раскладывают детей; Fusion красит; события — Qt-сигналы + хелперы `GuiEvent`.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| Якоря 0–11 | `core/gui/wid/base.py` | `ANCHOR_TOP_LEFT` 0 … `ANCHOR_STRETCH_HEIGHT` 11 |
| `AnchorPresetSelector` | `core/gui/wid/base.py` | Пикер 3x4, сигнал `anchor_changed` |
| `HorizontalLayoutComponent` / `VerticalLayoutComponent` / `GridLayoutComponent` | `core/components/gui/layout/` | `QHBoxLayout` / `QVBoxLayout` / `QGridLayout` через `_sync_layout(canvas)` |
| `LayoutElementComponent` | `core/components/gui/layout/` | Элемент раскладки `_sync_layout_element(widget)` |
| `TooltipComponent` | `core/components/gui/controls/tooltip_component.py` | Тултип соседнего виджета: `text` (+ `_duration` сек, `_rich_text`) через `setToolTip` / `setToolTipDuration`; только один на сущность (`_allow_multiple = False`) |
| `GuiEvent` / `ClickEvent` / `ValueChangeEvent` / `HoverEvent` / `FocusEvent` | `core/gui/events.py` | `connect`, `disconnect`, `emit`, `once` |
| `apply_fusion_style` / `get_widget_base_style` | `core/gui/wid/base.py` | Стили по object-name (`GuiButton`, `GuiLabel`, …) |
| Миксины overlay/events канваса | `core/gui/canvasp/overlay.py`, `core/gui/canvasp/events.py` | Отрисовка сетки, drag/resize, рамка выбора, pan/zoom |

## Использование в редакторе

1. Задайте `anchor` каждому виджету (по умолч. top-left; фон — stretch-all).
2. Добавьте `Horizontal/Vertical/GridLayoutComponent` на контейнер для автораскладки детей.
3. Настройте `LayoutElementComponent` на детях для stretch/fill.

## Использование в коде

```python
btn = gui.button(x=10, y=10, w=120, h=40, text="OK")
btn._anchor = 2  # ANCHOR_TOP_RIGHT
btn.update_anchor(canvas.width(), canvas.height())
btn.clicked.connect(lambda: print("ok"))
```

## Связанные

- Обзор GUI, Канвас, Контролы, Контейнеры

## Проверено по

- `core/gui/wid/base.py`, `core/gui/events.py`, `core/gui/canvasp/`
