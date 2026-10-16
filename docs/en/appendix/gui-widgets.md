# GUI Widgets Catalog

> `WIDGET_REGISTRY` has 34 keys (32 classes; `Chart`/`ChartPlotter` alias); `NATIVE_WIDGET_TYPES` has 30 names. Base classes below are the literal Qt superclasses — their standard Qt signals (`clicked`, `toggled`, `valueChanged`, `textChanged`, `currentIndexChanged`, …) are therefore available.

[Home](../../index.md)

## Registry vs native set

`core/gui/wid/__init__.py:14`: `WIDGET_REGISTRY = {Panel, Label, Button, Slider, TextInput, Toggle, ProgressBar, Dropdown, ScrollPanel, Image, RadioButton, ListWidget, TableWidget, TreeWidget, TabWidget, GroupBox, SpinBox, DoubleSpinBox, TextEdit, HtmlView, Dial, Splitter, StackedWidget, ToolBox, Calendar, LCDNumber, PlainText, ScrollBar, ToolButton, FontCombo, MdiArea, MdiSubWindow, Chart, ChartPlotter}` (34 keys; `Chart` and `ChartPlotter` point at the same `ChartPlotter` class). `core/gui/canvasp/canvas.py:18-24`: `NATIVE_WIDGET_TYPES` = the same minus `MdiArea`, `MdiSubWindow`, `Chart`, `ChartPlotter` (30 names).

## Widget catalog

| widget | base | one line |
|---|---|---|
| `Panel` | `QFrame` | Absolute-positioned container root (`containers.py:13`) |
| `Label` | `QLabel` | Static/rich text display (`text.py:12`) |
| `Button` | `QPushButton` | Push button, `clicked` signal (`buttons.py:12`) |
| `Slider` | `QSlider` | Integer slider, `valueChanged` (`sliders.py:13`) |
| `TextInput` | `QLineEdit` | Single-line edit, `textChanged` (`text.py:54`) |
| `Toggle` | `QCheckBox` | Checkable toggle, `toggled` (`buttons.py:54`) |
| `ProgressBar` | `QProgressBar` | Read-only progress display (`sliders.py:78`) |
| `Dropdown` | `QComboBox` | Item popup, `currentIndexChanged` (`lists.py:142`) |
| `ScrollPanel` | `QScrollArea` | Scrollable container (`containers.py:177`) |
| `Image` | `QWidget` | Image painter widget (`views.py:13`) |
| `RadioButton` | `QRadioButton` | Exclusive option button (`buttons.py:97`) |
| `ListWidget` | `QListWidget` | Item list (`lists.py:12`) |
| `TableWidget` | `QTableWidget` | Cell grid (`lists.py:98`) |
| `TreeWidget` | `QTreeWidget` | Hierarchical tree (`lists.py:56`) |
| `TabWidget` | `QTabWidget` | Tabbed pages (`containers.py:97`) |
| `GroupBox` | `QGroupBox` | Titled group container (`containers.py:54`) |
| `SpinBox` | `QSpinBox` | Integer stepper, `valueChanged` (`sliders.py:173`) |
| `DoubleSpinBox` | `QDoubleSpinBox` | Float stepper, `valueChanged` (`sliders.py:219`) |
| `TextEdit` | `QTextEdit` | Multi-line rich edit (`text.py:118`) |
| `HtmlView` | `QTextBrowser` | HTML viewer (`views.py:77`) |
| `Dial` | `QDial` | Rotary dial, `valueChanged` (`sliders.py:125`) |
| `Splitter` | `QSplitter` | Resizable split panes (`containers.py:158`) |
| `StackedWidget` | `QStackedWidget` | Indexed page stack (`containers.py:139`) |
| `ToolBox` | `QToolBox` | Accordion pages (`containers.py:278`) |
| `Calendar` | `QCalendarWidget` | Date picker (`views.py:123`) |
| `LCDNumber` | `QLCDNumber` | LCD-style numeric display (`views.py:142`) |
| `PlainText` | `QPlainTextEdit` | Plain multi-line edit (`text.py:99`) |
| `ScrollBar` | `QScrollBar` | Standalone scrollbar, `valueChanged` (`sliders.py:59`) |
| `ToolButton` | `QToolButton` | Toolbar button with tooltip sync (`buttons.py:140`; component mirrors `_tooltip` via `toolTip()`/`setToolTip`, `toolbutton_component.py:56-57`) |
| `FontCombo` | `QFontComboBox` | Font-family picker (`lists.py:189`) |
| `MdiArea` | `QMdiArea` | MDI workspace (registry-only, non-native) |
| `MdiSubWindow` | `QMdiSubWindow` | MDI child window (registry-only, non-native) |
| `Chart` / `ChartPlotter` | `QWidget` | Custom chart plotter, registry alias ×2 (`views.py:161`) |

Event bridging: `GuiEvent` subclasses `ClickEvent`, `ValueChangeEvent`, `HoverEvent`, `FocusEvent` (`core/gui/events.py:34-46`); runtime forwards `textChanged/valueChanged/currentTextChanged/…` into component callbacks (`core/components/gui/layout/basewidget/runtime.py:213`).

## Authoring API

`GuiApi` (`core/gui/api.py:21-119`): `panel/label/button/slider/input/image/toggle/progress/dropdown/scroll/mdi_area` factories (each `add_widget` onto the canvas), `clear()`, `save(path)` → `canvas.save_to_file`, `load(path)` → `canvas.load_from_file`. `GuiCanvas` (`canvasp/canvas.py:179-285`): `add_widget(widget, parent)`, `_root` full-stretch `Panel`, `save_to_file` / `load_from_file` JSON (`type`-keyed via `WIDGET_REGISTRY.get(wtype)`, default `"Button"` — `canvas.py:266-267`), signals `widget_selected/widget_changed/scene_modified/copy_requested/paste_requested/zoom_changed` (`canvas.py:28-33`). `TooltipComponent` (`controls/tooltip_component.py:14-56`): `text=""`, `_duration=0.0`, `_rich_text=True`; `on_update` pushes `setToolTip[_duration]` onto the entity's widget ref.

## Verified against

- `core/gui/wid/__init__.py:7-15` (34-key registry), `core/gui/canvasp/canvas.py:18-33, 179-285` (30 native types, canvas API + signals)
- `core/gui/wid/{buttons,text,sliders,lists,containers,views}.py` (each base class above), `core/gui/api.py:37-119` (factories, save/load)
- `core/gui/events.py:34-46`, `core/components/gui/layout/basewidget/runtime.py:213`, `core/components/gui/controls/tooltip_component.py`, `toolbutton_component.py:56-87`, `core/components/gui/__init__.py:44,84,194`
