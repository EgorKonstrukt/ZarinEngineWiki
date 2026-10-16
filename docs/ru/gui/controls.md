# Контролы

Только виджеты из `core/gui/wid/`: кнопки, текст, слайдеры, списки и вью. Каждая позиция — одна строка.

## Свойства / API

| Виджет | Файл | База |
|---|---|---|
| `Button` — кнопка с `text`. | `core/gui/wid/buttons.py` | `QPushButton` |
| `Toggle` — чекбокс с `text`, `checked`. | `core/gui/wid/buttons.py` | `QCheckBox` |
| `RadioButton` — радио-опция с `text`, `checked`. | `core/gui/wid/buttons.py` | `QRadioButton` |
| `ToolButton` — малая тулбар-кнопка с `text`. | `core/gui/wid/buttons.py` | `QToolButton` |
| `Label` — статический текст `text`. | `core/gui/wid/text.py` | `QLabel` |
| `TextInput` — однострочный ввод `text`, `placeholder`. | `core/gui/wid/text.py` | `QLineEdit` |
| `PlainText` — многострочный plain-текст. | `core/gui/wid/text.py` | `QPlainTextEdit` |
| `TextEdit` — многострочный rich-текст. | `core/gui/wid/text.py` | `QTextEdit` |
| `Slider` — горизонтальный слайдер `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QSlider` |
| `ScrollBar` — скроллбар. | `core/gui/wid/sliders.py` | `QScrollBar` |
| `ProgressBar` — прогресс `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QProgressBar` |
| `Dial` — крутилка `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QDial` |
| `SpinBox` — int-счётчик. | `core/gui/wid/sliders.py` | `QSpinBox` |
| `DoubleSpinBox` — float-счётчик `decimals`. | `core/gui/wid/sliders.py` | `QDoubleSpinBox` |
| `ListWidget` — список строк `items`. | `core/gui/wid/lists.py` | `QListWidget` |
| `TreeWidget` — дерево с заголовком `Tree`. | `core/gui/wid/lists.py` | `QTreeWidget` |
| `TableWidget` — таблица `rows`, `cols`. | `core/gui/wid/lists.py` | `QTableWidget` |
| `Dropdown` — комбобокс `items`, `current`. | `core/gui/wid/lists.py` | `QComboBox` |
| `FontCombo` — выбор шрифта. | `core/gui/wid/lists.py` | `QFontComboBox` |
| `Image` — картинка `source`. | `core/gui/wid/views.py` | `QWidget` + `QPixmap` |
| `HtmlView` — HTML `html`. | `core/gui/wid/views.py` | `QTextBrowser` |
| `Calendar` — календарь. | `core/gui/wid/views.py` | `QCalendarWidget` |
| `LCDNumber` — LCD-индикатор. | `core/gui/wid/views.py` | `QLCDNumber` |
| `ChartPlotter` — хост графиков (`type: Chart`). | `core/gui/wid/views.py` | `QWidget` |

Общие поля: `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag`, `serialize()` / `deserialize()`.

## Использование в редакторе

1. Добавьте парный компонент (`ButtonComponent`, `LabelComponent`, `SliderComponent`, …).
2. Правьте `text` / `value` / `items` / `source` в Inspector.
3. Сигналы Qt (`clicked`, `valueChanged`, `currentIndexChanged`, `textChanged`); хелперы `GuiEvent` в `core/gui/events.py`.

## Использование в коде

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
btn = gui.button(x=10, y=10, w=120, h=40, text="Огонь")
btn.clicked.connect(lambda: print("fire"))
sld = gui.slider(x=10, y=60, w=200, h=30, min_val=0, max_val=100)
sld.valueChanged.connect(lambda v: print(v))
```

## Связанные

- Обзор GUI, Канвас, Контейнеры, Раскладка и события

## Проверено по

- `core/gui/wid/buttons.py`, `text.py`, `sliders.py`, `lists.py`, `views.py`, `events.py`, `api.py`
