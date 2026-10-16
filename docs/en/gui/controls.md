# Controls

Only widgets that exist in `core/gui/wid/`: buttons, text, sliders, lists and views. Each entry is one line.

## Properties / API

| Widget | Source file | Base |
|---|---|---|
| `Button` — clickable push button with `text`. | `core/gui/wid/buttons.py` | `QPushButton` |
| `Toggle` — checkable box with `text`, `checked`. | `core/gui/wid/buttons.py` | `QCheckBox` |
| `RadioButton` — exclusive option with `text`, `checked`. | `core/gui/wid/buttons.py` | `QRadioButton` |
| `ToolButton` — small toolbar button with `text`. | `core/gui/wid/buttons.py` | `QToolButton` |
| `Label` — static text `text`. | `core/gui/wid/text.py` | `QLabel` |
| `TextInput` — single-line edit with `text`, `placeholder`. | `core/gui/wid/text.py` | `QLineEdit` |
| `PlainText` — plain multi-line edit with `text`. | `core/gui/wid/text.py` | `QPlainTextEdit` |
| `TextEdit` — rich multi-line edit with `text`. | `core/gui/wid/text.py` | `QTextEdit` |
| `Slider` — horizontal slider with `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QSlider` |
| `ScrollBar` — scrollbar. | `core/gui/wid/sliders.py` | `QScrollBar` |
| `ProgressBar` — progress with `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QProgressBar` |
| `Dial` — rotary dial with `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QDial` |
| `SpinBox` — integer spinner with `min`, `max`, `value`. | `core/gui/wid/sliders.py` | `QSpinBox` |
| `DoubleSpinBox` — float spinner with `min`, `max`, `value`, `decimals`. | `core/gui/wid/sliders.py` | `QDoubleSpinBox` |
| `ListWidget` — string list with `items`. | `core/gui/wid/lists.py` | `QListWidget` |
| `TreeWidget` — tree with header `Tree`. | `core/gui/wid/lists.py` | `QTreeWidget` |
| `TableWidget` — table with `rows`, `cols`. | `core/gui/wid/lists.py` | `QTableWidget` |
| `Dropdown` — combo box with `items`, `current`. | `core/gui/wid/lists.py` | `QComboBox` |
| `FontCombo` — font picker. | `core/gui/wid/lists.py` | `QFontComboBox` |
| `Image` — pixmap view with `source` path. | `core/gui/wid/views.py` | `QWidget` + `QPixmap` |
| `HtmlView` — HTML browser with `html`. | `core/gui/wid/views.py` | `QTextBrowser` |
| `Calendar` — date picker. | `core/gui/wid/views.py` | `QCalendarWidget` |
| `LCDNumber` — LCD numeric display. | `core/gui/wid/views.py` | `QLCDNumber` |
| `ChartPlotter` — chart host (`type: Chart`), lazy `ChartWidget` from plotter plugin. | `core/gui/wid/views.py` | `QWidget` + `QVBoxLayout` |

Common fields on every control: `x`, `y`, `w`, `h`, `anchor`, `z_index`, `tag`, plus `serialize()` / `deserialize()` round-trip.

## Usage in Editor

1. Add the matching component (`ButtonComponent`, `LabelComponent`, `SliderComponent`, …) to an entity.
2. Edit `text` / `value` / `items` / `source` in the Inspector; canvas creates the Qt widget.
3. Wire Qt signals (`clicked`, `valueChanged`, `currentIndexChanged`, `textChanged`) from code; `GuiEvent` helpers (`ClickEvent`, `ValueChangeEvent`) exist in `core/gui/events.py`.

## Usage in Code

```python
from core.gui.api import GuiApi

gui = GuiApi(name="default")
btn = gui.button(x=10, y=10, w=120, h=40, text="Fire")
btn.clicked.connect(lambda: print("fire"))
sld = gui.slider(x=10, y=60, w=200, h=30, min_val=0, max_val=100)
sld.valueChanged.connect(lambda v: print(v))
dd = gui.dropdown(x=10, y=100, w=200, h=32, items=["A", "B", "C"])
```

## Related

- GUI Overview, Canvas, Containers, Layout and Events

## Verified against

- `core/gui/wid/buttons.py`, `core/gui/wid/text.py`, `core/gui/wid/sliders.py`
- `core/gui/wid/lists.py`, `core/gui/wid/views.py`, `core/gui/events.py`, `core/gui/api.py`
