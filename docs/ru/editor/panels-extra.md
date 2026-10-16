# Панели анимации, скриптов, префабов, меша, VCS

> Доки-сателлиты в `editor/panels/`: вид Play, анимация, правка скриптов, префабы/библиотека, меш, пакеты, VCS плюс поддерживающие их мелкие диалоги/виджеты. Все имена классов/методов ниже прочитаны из указанных файлов.

## API / Properties

| Панель | Расположение | Проверенный API |
|---|---|---|
| Док Play + рантайм-вид | `panels/play/dock.py`, `viewport.py`, `viewport_base.py`, `fallback_view.py` | Рендер-поверхность Play-режима |
| Редактор клипов | `panels/anim/panel.py` | Поверхность правки ключей клипа |
| Граф аниматора | `panels/animator/panel.py`, `graph.py`, `params.py` | Канва стейт-машины + список параметров |
| Док террейна | `panels/terrain/panel.py`, `graph_widget.py` | Встраивает канву нод террейна (см. Граф террейна) |
| Time travel | `panels/timetravel/panel.py`, `recorder.py`, `playback.py` | Запись + скруб истории сессии |
| VCS | `panels/vcsp/panel.py`, `git.py`, `commit.py`, `diff.py`, `widgets.py` | Git-статус/коммит/дифф внутри редактора |
| Меш-редактор | `panels/meshedit/panel.py`, `ops.py`, `widgets.py` | Операции с мешем (поддержан `plugins/mesh_editor_plugin.py` MeshEditorPlugin) |
| Редактор скриптов | `panels/script_editor_panel/panel.py` + `edit/`, `tabsp/`, `wid/` | Многотабовый редактор кода: `highlighter.py`, `folding.py`, `gutter.py`, `diagnostics.py`, `inspection.py`, `api_words.py`, `doc_popup.py`/`doc_provider.py`, `blame.py`, `ot.py` (коллаб OT), `stripe.py`, `theme.py`, `vision.py`, `smooth.py` |
| Реестр скриптов | `panels/scripts_panel.py`, `syntax_config.py` | Список скриптов + настройки подсветки |
| Редактор префабов | `panels/prefab_editor_panel.py` | Вкладка префаба (открывается из `handlersp/prefab.py`) |
| Библиотечные паки | `panels/library_pack_dialog.py` | Диалог-браузер паков |
| Менеджер пакетов | `panels/pkgmgr/panel.py`, `ops.py`, `bridge.py`, `dialogs.py` (+ `pkgcore/`) | Установка/удаление пакетов |
| Менеджер плагинов | `panels/plugin_manager_panel.py`: `PluginManagerPanel(engine)` + `_refresh()`, `_toggle_plugin(row, state)`, `_load_plugin()`, `_build_plugin()`, `_package_zplugin()`, `_new_library_pack()` | Вкл/выкл, загрузка, сборка, упаковка |
| Упаковщик плагинов | `panels/plugin_package_panel.py` | UI упаковки `.zplugin` |
| Создание сущностей | `panels/add_entity_menu.py`, `panels/node_palette.py` | Диалог «Add Entity...» + палитра нод |
| Обвязка сцен | `editor/scene_async.py`, `editor/scene_toolbar.py`, `editor/ipc_server.py` | Асинхронная загрузка сцен, тулбар сцены, внешний IPC |
| Оформление app | `editor/splash.py`, `editor/bug_report.py`, `editor/project_manager.py`, `editor/file_associations.py`, `editor/shellmenu/` | Сплэш, репортер багов, привязки файлов, пункты шелла ОС |
| Хелперы | `editor/cam/`, `editor/entframe/`, `editor/hover/`, `editor/gloff/`, `editor/gui_editor/`, `editor/matwiz/`, `editor/picker/`, `editor/icons.py`, `editor/constants.py`, `editor/keybinding_widget.py`, `editor/sphere_renderer.py` | Хелперы камеры/фрейма/ховера, GUI-редактор, пикеры, реестры иконок и констант |

## Usage in Editor

Каждый док переключается из меню View и хранит геометрию через `load_config`. Вкладки скриптов зеркалят таб-бар скриптов главного окна (удалённые правки пиров приходят через `ot.py`). Панель Animator правит состояния/переходы, чьи поля рисуются во вложенных редакторах Inspector. Time travel пишет во время Play и скрубит после. Коммит/дифф VCS работают на корне проекта. Дополнительных глобальных хоткеев для этих доков в коде нет, кроме набора главного окна.

## Usage in Code

```python
panel = PluginManagerPanel(engine, parent=mw)
panel._refresh()
panel._toggle_plugin(row, state)
```

## Related

- Главное окно, Инспектор (вложенные редакторы аниматора), Панель проекта, Доки коллаборации и MCP
- Плагины: PluginBase Lifecycle, Plugin Manager, упаковка `.zplugin`

## Verified against

- Листинг каталога `editor/panels/` (все имена выше), `editor/panels/plugin_manager_panel.py`
- `editor/scene_async.py`, `editor/scene_toolbar.py`, `editor/ipc_server.py`, `editor/splash.py`, `editor/bug_report.py`, `editor/file_associations.py`
