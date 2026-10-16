# События движка

> Шина движка — три метода `Engine` (`EngineEventsMixin`): `on(event, callback)`, `off(event, callback)`, `_emit_event(event, data)`. Исключения колбэков ловятся и логируются; при ≥ 10 лисенерах генерация расходится по общему тредпулу.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`core/engine/eng/events.py` реализует шину: `on` дописывает в `_event_listeners[event]`; `off` удаляет; `_emit_event` при менее чем 10 лисенерах вызывает синхронно (каждый в try/except → `Event callback error '<event>'`), иначе сабмитит в общий пул (`core.ecs.pool.general`) и ждёт через `as_completed`. `_defer_gui` постит через `_gui_poster`, когда задан, иначе выполняет инлайново.

События, генерируемые движком (все проверены по местам генерации):

| событие | пейлоад | откуда генерится |
|---|---|---|
| `history_changed` | `None` | `CommandHistory.execute` / `seek` всегда; `undo` / `redo` при лисенерах |
| `scene_loaded` | `Scene` | пути new/open/load сцен, Play-восстановление, восстановление time-travel |
| `scene_saved` | `Scene` / сцена | пути сохранения (включая daemon save-воркер) |
| `entity_created` | сущность | создание сущности из панели проекта |
| `play_start` / `play_stop` | `None` | `Engine.start_play` / `stop_play` |

Хуки плагинов — отдельный канал (no-op методы `PluginBase`, раздуваемые `PluginManager.notify_*`): `on_viewport_ready(viewport)` (вызывается напрямую из сеттера `Engine.viewport`), `on_scene_loaded(scene)` / `on_scene_unloaded(scene)`, `on_project_opened()`, `on_play_start()` / `on_play_stop()` (вызываются рядом с событиями `play_start`/`play_stop`), `on_main_window_ready(window)` (через `Engine.set_main_window`). `step(dt)` / `pre_step(dt)` — покадровые тики плагинов, не события. Примеры хуков в дереве: `example_plugin.py` (`on_scene_loaded`, `on_play_start/stop`), `physics_drag_plugin.py` (+ `on_viewport_ready`), `qt_quick_plugin` (+ `on_scene_unloaded`).

## Свойства

| имя | сигнатура | описание |
|---|---|---|
| `on(event, cb)` | метод | Подписка |
| `off(event, cb)` | метод | Отписка (отсутствует → no-op) |
| `_emit_event(event, data)` | метод | Синхронно при < 10 лисенерах, иначе через пул |
| `history_changed` | событие | Сигнал изменения undo-стека |
| `scene_loaded` / `scene_saved` | события | Сигнал жизненного цикла сцены |
| `play_start` / `play_stop` | события | Сигнал Play-режима |
| `PluginBase.on_*` | хуки | Параллельный канал жизненного цикла плагинов |

## Использование в редакторе

Панель иерархии обновляется по `engine.on("history_changed", ...)`; time-travel playback и вкладки сцен генерят `scene_loaded` после подмен, чтобы все панели перепривязались. Код редактора подписывается через `on` и никогда не вызывает `notify_*` плагинов напрямую (это работа `PluginManager`).

## Использование в коде

```python
def refresh(_):
    print("history changed")

engine.on("history_changed", refresh)
engine.on("scene_loaded", lambda scene: print("loaded:", scene))
engine.on("play_start", lambda _: print("play"))
# ...
engine.off("history_changed", refresh)
```

```python
from core.foundation.plugins.base import PluginBase

class MyPlugin(PluginBase):
    NAME = "MyPlugin"

    def on_scene_loaded(self, scene):
        print("scene:", scene)

    def on_play_start(self):
        print("play started")

    def on_main_window_ready(self, main_window):
        print("ui ready")
```

## Сериализация

События несут живые объекты (`Scene`, сущность) и никогда не сериализуются.

## Связанные страницы

- [Команды и Undo](./commands-undo.md)
- [Time Travel](./time-travel.md)
- [Жизненный цикл PluginBase](../../plugins/pluginbase.md)
- [Менеджер плагинов](../../plugins/plugin-manager.md)
- [Разбор example-плагина](../../plugins/example-plugin.md)

## Проверено по

- `core/engine/eng/events.py` (`on`, `off`, `_emit_event`, порог fan-out в пул, `_defer_gui`)
- `core/engine/eng/engine.py` (события `start_play`/`stop_play` + `notify_play_start/stop`, `set_main_window`, сеттер `viewport`)
- `core/engine/eng/scenes.py` (генерации `scene_loaded` / `scene_saved`)
- `core/foundation/cmd/history.py` (политика генерации `history_changed`)
- `core/foundation/plugins/base.py` (все хуки `PluginBase.on_*`)
- `core/foundation/plugins/lifecycle.py` (fan-out `notify_*`)
- `editor/panels/hierarchy/panel.py` (подписка `history_changed`)
- `editor/panels/project/openers.py` (генерация `entity_created`)
