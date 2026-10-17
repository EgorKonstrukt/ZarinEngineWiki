# GUI из кода

Управление интерфейсом из игрового кода: кнопки, лейблы, канвас и строители `GuiApi`. Имена компонентов для поиска: `"ButtonComponent"`, `"LabelComponent"` и т.д.

## Компоненты виджетов

Общие поля всех виджетов (сериализуются, редактируются в инспекторе):

| Поле | По умолчанию | Замечания |
|---|---|---|
| `widget_text` | `"Button"` / `"Label"` / … | Синхронизируется с живым Qt-виджетом через `setText`/`text()`. |
| `widget_width` / `widget_height` | по типу (кнопка 120×36, лейбл 100×30) | Логический размер, масштабируется зумом в экранный. |
| `_anchor` | `0` | Id пресет-анкора. |
| `_visible` | `True` | Флаг видимости. |
| `_bg_color` / `_text_color` / `_border_color` | `None` | RGB-списки, через stylesheet. |
| `_font_size` / `_font_bold` / `_opacity` | `14.0` / `False` / `1.0` | Внешний вид. |

Доступ к живому виджету:

| Член | Поведение |
|---|---|
| `comp._widget_ref` | Живой `QWidget` (например `Button(QPushButton)`), либо `None` до создания/после уничтожения. |
| `comp.sync_to_widget()` | Проталкивает состояние компонента (геометрия, текст, стиль) в живой виджет. |
| `comp.update_from_widget()` | Забирает геометрию/текст обратно (путь перетаскивания в редакторе). |
| `Button.clicked` | Нативный Qt-сигнал `QPushButton.clicked` — подключайте слот через `connect`. У других виджетов правки (`textChanged`/`valueChanged`/`toggled`) сами синкаются обратно. |

Позиция виджета берётся из `Transform.local_position` сущности (x/y); размер — из `widget_width`/`widget_height`.

## Канвас и GuiApi

| Сигнатура | Поведение |
|---|---|
| `GuiCanvas.add_widget(widget, parent_widget=None) -> QWidget` | Подвешивает под `parent_widget` или корень канваса, показывает, чинит анкор/z-порядок. |
| `GuiCanvas.remove_widget(widget)` / `clear()` / `get_all_widgets()` | Удаление, полная очистка, список корневых. |
| `GuiCanvas.save_to_file(path)` / `load_from_file(path)` | JSON round-trip всего дерева виджетов. Сигналы: `widget_selected`, `widget_changed`, `scene_modified`, `copy_requested`, `paste_requested`, `zoom_changed`. |
| `GuiApi.get(name="default") -> GuiApi` | Именованный реестр канвасов. |
| `api.label/button/panel/slider/input/image/toggle/progress/dropdown/scroll/mdi_area(...)` | Строитель + авто-`add_widget`, возвращает виджет. |
| `api.clear()` / `api.save(path)` / `api.load(path)` | Очистка канваса и файловый round-trip. |

## Пример HUD счётчика (полный, рабочий)

Лейбл показывает счёт, кнопка его увеличивает — Qt-сигналы из скрипта поведения:

```python
class CounterHud:
    start_score: int = 0

    def on_start(self):
        scene = self._entity._scene
        self.score = int(self.start_score)
        self.label = scene.get_entity_by_name("ScoreLabel")
        btn_entity = scene.get_entity_by_name("ScoreButton")
        self._refresh()
        if btn_entity is not None:
            btn = btn_entity.get_component_by_name("ButtonComponent")
            w = btn._widget_ref if btn is not None else None
            if w is not None:
                try:
                    w.clicked.disconnect()
                except Exception:
                    pass
                w.clicked.connect(self._on_click)

    def _on_click(self):
        self.score += 1
        self._refresh()

    def _refresh(self):
        if self.label is None:
            return
        comp = self.label.get_component_by_name("LabelComponent")
        if comp is None:
            return
        comp.widget_text = f"Score: {self.score}"
        comp.sync_to_widget()

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.R):
            self.score = 0
            self._refresh()
```

Поля инспектора: `start_score` (int). Нужны сущности `ScoreLabel` (LabelComponent) и `ScoreButton` (ButtonComponent) где-то в сцене.

Построение UI чисто кодом через `GuiApi`:

```python
class MenuBuilder:
    def on_start(self):
        from core.gui.api import GuiApi
        api = GuiApi.get("default")
        if api is None:
            Logger.warning("MenuBuilder: no default GuiApi")
            return
        title = api.label(x=300, y=40, w=200, h=40, text="PAUSED")
        btn = api.button(x=300, y=120, w=200, h=48, text="Resume")
        btn.clicked.connect(self._on_resume)
        self._built = [title, btn]

    def _on_resume(self):
        Logger.info("Resume pressed")

    def on_destroy(self):
        from core.gui.api import GuiApi
        api = GuiApi.get("default")
        if api is not None:
            api.clear()
```

Сохранение раскладки на диск и восстановление:

```python
api.save("assets/ui/hud.json")   # под капотом GuiCanvas.save_to_file
api.load("assets/ui/hud.json")   # под капотом GuiCanvas.load_from_file
```

## Связанные страницы

- `entity-api.md` — поиск сущностей виджетов по имени и кэширование компонентов.
- `input-reference.md` — `KeyCode.R`, коды мыши для UI-скриптов.
- `templates.md` — счётчик HUD как стартовый шаблон.

## Проверено по коду

- `core/components/gui/controls/button_component.py:12-23` (`ButtonComponent`, `widget_text="Button"`), `label_component.py:12-39` (`LabelComponent`, `_halign`).
- `core/components/gui/layout/basewidget/component.py:12-119` (общие поля + serialize), `runtime.py:87-140` (создание виджета + `setText(widget_text)`), `:142-208` (пути синка), `:230-276` (обратный забор).
- `core/gui/wid/buttons.py` (`Button(QPushButton)` ⇒ нативный сигнал `clicked`).
- `core/gui/api.py:24-111` (строители `GuiApi`, `save`/`load`), `core/gui/canvasp/canvas.py:28-33` (сигналы), `:179-206` (add/remove/clear), `:279-285` (файловый round-trip).
