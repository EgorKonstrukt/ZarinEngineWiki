# Каталог GUI-виджетов

> В `WIDGET_REGISTRY` 34 ключа (32 класса; `Chart`/`ChartPlotter` — алиас); в `NATIVE_WIDGET_TYPES` 30 имён. Базовые классы ниже — буквальные Qt-суперклассы, потому их стандартные Qt-сигналы (`clicked`, `toggled`, `valueChanged`, `textChanged`, `currentIndexChanged`, …) доступны.

[Home](../../index.md)

## Реестр vs нативный сет

`core/gui/wid/__init__.py:14`: `WIDGET_REGISTRY = {Panel, Label, Button, Slider, TextInput, Toggle, ProgressBar, Dropdown, ScrollPanel, Image, RadioButton, ListWidget, TableWidget, TreeWidget, TabWidget, GroupBox, SpinBox, DoubleSpinBox, TextEdit, HtmlView, Dial, Splitter, StackedWidget, ToolBox, Calendar, LCDNumber, PlainText, ScrollBar, ToolButton, FontCombo, MdiArea, MdiSubWindow, Chart, ChartPlotter}` (34 ключа; `Chart` и `ChartPlotter` указывают на один класс `ChartPlotter`). `core/gui/canvasp/canvas.py:18-24`: `NATIVE_WIDGET_TYPES` = то же минус `MdiArea`, `MdiSubWindow`, `Chart`, `ChartPlotter` (30 имён).

## Каталог виджетов

| виджет | база | одна строка |
|---|---|---|
| `Panel` | `QFrame` | Контейнер с абсолютным позиционированием, корень (`containers.py:13`) |
| `Label` | `QLabel` | Статик/rich-текст (`text.py:12`) |
| `Button` | `QPushButton` | Кнопка, сигнал `clicked` (`buttons.py:12`) |
| `Slider` | `QSlider` | Целочисленный слайдер, `valueChanged` (`sliders.py:13`) |
| `TextInput` | `QLineEdit` | Однострочный ввод, `textChanged` (`text.py:54`) |
| `Toggle` | `QCheckBox` | Чекбокс-тогл, `toggled` (`buttons.py:54`) |
| `ProgressBar` | `QProgressBar` | Read-only индикатор прогресса (`sliders.py:78`) |
| `Dropdown` | `QComboBox` | Выпадающий список, `currentIndexChanged` (`lists.py:142`) |
| `ScrollPanel` | `QScrollArea` | Скроллируемый контейнер (`containers.py:177`) |
| `Image` | `QWidget` | Виджет-рисовальщик изображений (`views.py:13`) |
| `RadioButton` | `QRadioButton` | Эксклюзивная опция (`buttons.py:97`) |
| `ListWidget` | `QListWidget` | Список айтемов (`lists.py:12`) |
| `TableWidget` | `QTableWidget` | Сетка ячеек (`lists.py:98`) |
| `TreeWidget` | `QTreeWidget` | Иерархическое дерево (`lists.py:56`) |
| `TabWidget` | `QTabWidget` | Вкладки (`containers.py:97`) |
| `GroupBox` | `QGroupBox` | Контейнер с заголовком (`containers.py:54`) |
| `SpinBox` | `QSpinBox` | Целочисленный степпер, `valueChanged` (`sliders.py:173`) |
| `DoubleSpinBox` | `QDoubleSpinBox` | Флоат-степпер, `valueChanged` (`sliders.py:219`) |
| `TextEdit` | `QTextEdit` | Многострочный rich-редактор (`text.py:118`) |
| `HtmlView` | `QTextBrowser` | Просмотр HTML (`views.py:77`) |
| `Dial` | `QDial` | Крутилка, `valueChanged` (`sliders.py:125`) |
| `Splitter` | `QSplitter` | Ресайзящиеся сплит-панели (`containers.py:158`) |
| `StackedWidget` | `QStackedWidget` | Стек страниц по индексу (`containers.py:139`) |
| `ToolBox` | `QToolBox` | Аккордеон страниц (`containers.py:278`) |
| `Calendar` | `QCalendarWidget` | Пикер даты (`views.py:123`) |
| `LCDNumber` | `QLCDNumber` | LCD-цифры (`views.py:142`) |
| `PlainText` | `QPlainTextEdit` | Многострочный plain-редактор (`text.py:99`) |
| `ScrollBar` | `QScrollBar` | Отдельный скроллбар, `valueChanged` (`sliders.py:59`) |
| `ToolButton` | `QToolButton` | Тулбар-кнопка с синком тултипа (`buttons.py:140`; компонент зеркалит `_tooltip` через `toolTip()`/`setToolTip`, `toolbutton_component.py:56-57`) |
| `FontCombo` | `QFontComboBox` | Пикер семейства шрифта (`lists.py:189`) |
| `MdiArea` | `QMdiArea` | MDI-воркспейс (только реестр, не нативный) |
| `MdiSubWindow` | `QMdiSubWindow` | MDI-дочернее окно (только реестр, не нативное) |
| `Chart` / `ChartPlotter` | `QWidget` | Кастомный плоттер графиков, алиас ×2 в реестре (`views.py:161`) |

Мост событий: сабклассы `GuiEvent` — `ClickEvent`, `ValueChangeEvent`, `HoverEvent`, `FocusEvent` (`core/gui/events.py:34-46`); рантайм форвардит `textChanged/valueChanged/currentTextChanged/…` в колбэки компонентов (`core/components/gui/layout/basewidget/runtime.py:213`).

## Авторский API

`GuiApi` (`core/gui/api.py:21-119`): фабрики `panel/label/button/slider/input/image/toggle/progress/dropdown/scroll/mdi_area` (каждая `add_widget` на канвас), `clear()`, `save(path)` → `canvas.save_to_file`, `load(path)` → `canvas.load_from_file`. `GuiCanvas` (`canvasp/canvas.py:179-285`): `add_widget(widget, parent)`, `_root` — full-stretch `Panel`, `save_to_file` / `load_from_file` JSON (по ключу `type` через `WIDGET_REGISTRY.get(wtype)`, дефолт `"Button"` — `canvas.py:266-267`), сигналы `widget_selected/widget_changed/scene_modified/copy_requested/paste_requested/zoom_changed` (`canvas.py:28-33`). `TooltipComponent` (`controls/tooltip_component.py:14-56`): `text=""`, `_duration=0.0`, `_rich_text=True`; `on_update` пушит `setToolTip[_duration]` на виджет-реф сущности.

## Проверено по

- `core/gui/wid/__init__.py:7-15` (реестр на 34 ключа), `core/gui/canvasp/canvas.py:18-33, 179-285` (30 нативных типов, API канваса + сигналы)
- `core/gui/wid/{buttons,text,sliders,lists,containers,views}.py` (каждый базовый класс выше), `core/gui/api.py:37-119` (фабрики, save/load)
- `core/gui/events.py:34-46`, `core/components/gui/layout/basewidget/runtime.py:213`, `core/components/gui/controls/tooltip_component.py`, `toolbutton_component.py:56-87`, `core/components/gui/__init__.py:44,84,194`
