# Иерархия

> `HierarchyPanel` (`editor/panels/hierarchy/panel.py`) — дерево сущностей в `QDockWidget`: поиск, состояние раскрытия, бейджи префабов, создание/репарент/удаление, зеркало выделения.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `HierarchyPanel` | `(engine: Engine, parent=None)` | Миксины `HierarchyDndMixin`, `HierarchyMenuMixin` |
| `refresh` / `_refresh` | `()` | Перестройка дерева с сохранением раскрытия |
| `load_config` | `(config) -> None` | Восстановление состояния панели |
| `_on_scene_loaded` | `(scene: Scene)` | Перепривязка при смене сцены |
| `_on_search` | `(text: str)` | Живой фильтр (`_entity_matches_filter`) |
| `_collapse_all` / `_toggle_embed_all` / `_toggle_compress` | `()` / `(checked: bool)` | Переключатели отображения |
| `_on_item_double_click` / `_on_item_changed` | `(item: QTreeWidgetItem, col: int)` | Переименование |
| `set_selected_entity` / `set_selected_entities` | `(entity)` / `(entities: list)` | API выделения (dnd-миксин) |
| `reveal_entity` / `flash_entity` | `(eid: str)` | Найти + подсветить |
| `_frame_selected` | `()` | Сфокусировать вьюпорт на выделении |
| `HierarchyTree` | `(panel: HierarchyPanel, parent=None)` в `tree.py` | `QTreeWidget` с drag/drop MIME, `dropEvent`, `keyPressEvent` |
| Меню: `_create_entity` / `_create_child` / `_create_primitive` / `_create_probuilder_primitive` / `_create_light` / `_create_camera` / `_create_from_component` | см. `menu.py` | Пункты создания |
| Меню: `_save_prefab` / `_apply_prefab` / `_revert_prefab` / `_unpack_prefab` | `(entity: Entity)` | Операции с префабами |
| DnD: `_on_reparent` / `_on_component_drop` / `_on_copy` / `_on_paste` / `_duplicate_entity` / `_delete_entity` | см. `dnd.py` | Репарент, копирование/вставка, дублирование, удаление |
| DnD: `_toggle_active` / `_toggle_lock` / `_toggle_embed` / `_toggle_active_selected` | см. `dnd.py` | Переключатели состояния |
| Хуки коллаборации: `_collab_sync_create` / `_collab_sync_delete` | `(entity)` / `(entity_id: str)` | Пересылка правок в сессию |

## Usage in Editor

Строка поиска фильтрует по имени (`_entity_matches_filter`); дабл-клик переименовывает; контекстное меню создаёт пустые/дочерние/примитивы/свет/камеру/сущности от компонента и выполняет save/apply/revert/unpack префаба. Перетаскивание меняет родителя (защита от циклов через `_is_descendant`); дроп компонента копирует или переносит его (`_on_component_drop`). Путь клавиши `Delete` идёт через `HierarchyTree.keyPressEvent`; копирование/вставка/дублирование сохраняют выделение через `_restore_selection`. Инстансы префабов показывают бейджи `_prefab_mark` с кэшем оверрайдов.

## Usage in Code

```python
panel.refresh()
panel.set_selected_entity(ent)
panel.reveal_entity(ent.id)
panel.flash_entity(ent.id)
```

## Related

- Инспектор (правка выделенной сущности), Вьюпорт (выделение + фокусировка), Панель проекта (ассеты префабов)
- Коллаборация (создание/удаление рассылаются пирам), Undo (хук обновления истории `_on_history_changed`)

## Verified against

- `editor/panels/hierarchy/panel.py`, `editor/panels/hierarchy/tree.py`, `editor/panels/hierarchy/menu.py`, `editor/panels/hierarchy/dnd.py`
