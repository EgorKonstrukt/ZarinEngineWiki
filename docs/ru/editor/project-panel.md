# Панель проекта и ассеты

> `ProjectPanel` (`editor/panels/project/`) — браузер ассетов: дерево папок + список/детали файлов, drag-and-drop, файловые операции с undo, визарды создания, хуки VCS, кэш миниатюр.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| Виды | `FileListWidget`, `FileDetailWidget`, `FolderTreeWidget` в `project/widgets.py` | Список иконок, дерево деталей, дерево папок; все принимают дропы |
| `DndMixin._move_or_copy_files` | `(paths, dest_dir=None, force_move=None)` | Ядро перемещения при дропе |
| `DndMixin` — операции | `_copy_selected/_cut_selected/_paste_clipboard/_duplicate_selected/_delete_selected/_copy_path` | Буфер + удаление |
| `DndMixin._on_entity_drop` | `(event)` | Дропы на сущности (путь назначения материала) |
| `FsOpsMixin._fs_copy/_fs_move/_fs_remove` | `(src, dst) -> bool` | Защищённые файловые примитивы |
| `FsOpsMixin` — undo файлов | `_push_file_undo(op)`, `_undo_file_op()`, `_redo_file_op()` | Отменяемые файловые операции |
| `FsOpsMixin._rename_item` | `(widget, item)` | Переименование на месте |
| `FsOpsMixin` — размеры | `_ensure_size_thread/_dir_size_loop/_request_dir_sizes/_on_dir_size_ready/_refresh_status_sizes` | Фоновый подсчёт размеров папок |
| `CreationMixin` — создатели | `_create_new_folder/_create_new_scene/_create_new_material/_create_new_physic_material/_create_new_animclip/_create_new_animcontroller/_create_new_script/_create_new_cython_script` | Визарды новых ассетов |
| `CreationMixin.set_project_root` | `(path: str)` | Перепривязка корня браузера |
| `VcsMixin` | `project/vcs.py` | Поверхность хуков контроля версий |
| Миниатюры | `editor/thumb_cache.py`, `editor/thumb_worker.py` | Асинхронная генерация миниатюр |

## Usage in Editor

Три панели: дерево папок (навигация + цель дропа), список файлов (иконки), вид деталей (колонки). Перетаскивание файлов между папками перемещает/копирует; перетаскивание ассетов во вьюпорт или на сущности инстанцирует/назначает. Правый клик создаёт Folder, Scene, Material, Physic Material, Animation Clip, Animation Controller, Script, Cython Script. Копирование/перемещение/удаление идут через `_push_file_undo` и участвуют в undo. Хлебные крошки (`_AddressBreadcrumb`) переключаются между режимом крошек и редактирования; строка поиска фильтрует текущую папку. Буквенных хоткеев для этих видов в коде нет — операции через меню/драг (у видов свой `keyPressEvent` для навигации по списку).

## Usage in Code

```python
panel.set_project_root(project_root)
panel._create_new_script()
panel._undo_file_op()   # откат последней файловой операции
panel._redo_file_op()
```

## Related

- Иерархия (инстанцирование префабов отсюда), Инспектор (цели дропа), Главное окно (маршрутизация дабл-клика)
- Кэш/воркер миниатюр, ассоциации файлов (`editor/file_associations.py`), шелл-меню ОС (`editor/shellmenu/`)

## Verified against

- `editor/panels/project/widgets.py`, `editor/panels/project/dnd.py`, `editor/panels/project/fs_ops.py`, `editor/panels/project/creation.py`, `editor/panels/project/vcs.py`
- `editor/thumb_cache.py`, `editor/thumb_worker.py`
