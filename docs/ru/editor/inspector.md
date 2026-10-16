# Инспектор

> Панель Inspector (`editor/inspector/`) — правка компонентов сущности: добавление/удаление/перестановка, типизированные виджеты полей, копирование/вставка значений, apply/revert префаба, редакторы animator-состояний.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `InspectorClipboardMixin.copy_component` | `(comp_name: str, comp_key: str = "")` | `inspector/panelp/clipboard.py` |
| `InspectorClipboardMixin.paste_component_values` | `()` | Вставка буфера на цель |
| `InspectorClipboardMixin.clear_clipboard` | `()` | Очистка буфера |
| `InspectorUndoMixin._add_component` | `(comp_name: str)` | Добавить с undo + пересылкой в коллаб |
| `InspectorUndoMixin._add_script_component` | `(script_path: str)` | Прицепить скриптовый компонент |
| `InspectorUndoMixin._remove_component` | `(comp_name: str, comp_key: str = "")` | Удалить с undo |
| `InspectorUndoMixin._move_component_up` / `_move_component_down` | `(comp_key: str)` | Перестановка |
| `InspectorUndoMixin._show_add_component_menu` | `()` | Открыть пикер |
| `InspectorUndoMixin._on_apply_prefab` / `_on_revert_prefab` | `()` | Фиксация/откат оверрайдов префаба |
| `InspectorUndoMixin._on_active_changed` / `_on_name_changed` / `_on_tag_changed` / `_on_layer_changed` | `(checked: bool)` / `(text: str)` / `(val: int)` | Поля шапки сущности |
| `ComponentPickerDialog` | `(entity, parent=None)` + `selected_result() -> Optional[dict]` | `inspector/component_picker.py`; категории + поиск |
| `EntityPickerDialog` | `(scene, parent=None)` + `selected_id() -> Optional[str]` | Пикер ссылки на сущность в `inspector/widgets.py` |
| `EntityPropertyTree` | `(scene, entity_id, ...)` + `set_property(path)`, `current_property()`, `property_label()` | `inspector/entity_property_picker.py` |
| `EntityPropertyDialog.exec_near_cursor` | `() -> int` + `result_entity_id/result_property/result_label` | Выбор свойства у курсора |
| `SourceViewerDialog` | `(file_path: str, line_number: int = 1, title="Source", parent=None)` | Переход к исходнику скрипта |
| Редакторы аниматора | `_build_animator_state_inspector(state)`, `_build_animator_transition_inspector(trans)`, строки условий add/remove | В `inspector/panelp/undo.py` |
| Виджеты полей | `_FocusSpinBox`, `_DragLabel`, `_ResourceDropLabel`, `_EntityDropLabel` | Числа перетаскиванием, дроп-зоны ассетов/сущностей |

## Usage in Editor

Add Component открывает `ComponentPickerDialog` (категории + поиск); шапка каждого компонента даёт копирование/вставку значений, перемещение вверх/вниз, удаление. Поля сущностей принимают drag-and-drop ассетов и ссылок на сущности. Состояния/переходы аниматора получают вложенные редакторы со строками условий. Шапка переключает active-флаг, имя, тег, слой. Горячих клавиш для этих действий в коде нет — вся работа в Inspector указателем.

## Usage in Code

```python
inspector.copy_component("Transform", "")
inspector.paste_component_values()
dlg = ComponentPickerDialog(entity, parent=mw)
if dlg.exec():
    inspector._add_component(dlg.selected_result()["name"])
```

## Related

- Иерархия (источник выделения), Панель проекта (источники дропа), Панель редактора префабов
- Скриптинг: Inspector Fields (схема атрибутов компонентов за виджетами)

## Verified against

- `editor/inspector/panelp/clipboard.py`, `editor/inspector/panelp/undo.py`
- `editor/inspector/component_picker.py`, `editor/inspector/entity_property_picker.py`, `editor/inspector/widgets.py`, `editor/inspector/source_viewer.py`
- `editor/panels/add_entity_menu.py`
