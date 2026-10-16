# Обзор редактора

> Редактор Zarin — приложение PySide6 (`EditorMainWindow`): 3D-вьюпорт, Hierarchy, Inspector, панель Project, Console и кнопки Play.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`main.py` собирает редактор: OpenGL 4.6 Core `QSurfaceFormat`, имя приложения `Zarin`, стиль Fusion, сплэш-скрин на 8 шагов, `Engine.initialize()`, плагины из `plugins/` и `plugins/user/`, `CollaborationManager`, затем `EditorMainWindow` с вьюпортом, назначенным в `engine.viewport`. Повторный запуск `main.py` пересылает `.zpes`-пути запущенному инстансу через `IpcServer`.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| window_class | class | `EditorMainWindow` | Главное окно (`editor/main_window`) |
| viewport | widget | ModernGL-based | 3D-вид реального времени; камера редактора с orbit, pan, fly |
| hierarchy | panel | `editor/panels/hierarchy` | Дерево сущностей; drag для репарента; контекстное меню по правому клику |
| inspector | panel | `editor/inspector` | Автогенерация полей по метаданным `InspectorField` (`float`, `int`, `bool`, `string`, `color`, `enum`, `Vec2/Vec3`, путь к ресурсу с picker, `curve`, list) |
| project | panel | `editor/panels/project` | Браузер ассетов; `Project → Create → Python Script`; даблклик по `.py` открывает редактор скриптов |
| console | panel | `editor/panels/console` | Вьюха лога поверх записей `Logger` |
| terminal | panel | `editor/panels/terminal` | Встроенный терминал |
| profiler_panel | panel | `editor/panels/profiler` | Оверлей таймингов кадра поверх `engine.profiler_data` |
| undo_history | panel | `editor/panels/undo_history_panel.py` | Вьюха поверх `CommandHistory` (`undo_text` / `redo_text`) |
| timetravel | panel | `editor/panels/timetravel` | Вьюха поверх кадров `SnapshotRecorder` |
| scripts_panel | panel | `editor/panels/scripts_panel.py` | Шейдер-граф док (`ScriptsPanel`, заголовок `Shaders`) |
| script_editor | panel | `editor/panels/script_editor_panel` | Редактор скриптов с проверкой синтаксиса `Check` / `Check Script` (`_check_current`) |
| splash_steps | int | `8` | `SplashScreen.set_total_steps(8)` в `main.py` |
| default_geometry | size | `1920x1080` | Центрирование при первом старте |

## Использование в редакторе

1. Hierarchy: правый клик → создать сущность; drag сущностей для репарента (`Entity.set_parent` с сохранением мира).
2. Inspector: `Add Component` перечисляет `ComponentRegistry.all()` по категориям; редактирование полей; тогл `enabled` у каждого компонента.
3. Project: создание скриптов, материалов, сцен; файлы сцен — `.zpes`.
4. Play: `Engine.start_play()`; повторное нажатие — стоп (`Engine.stop_play()` останавливает всё аудио через `AudioSourceManager.stop_all()`).

## Использование в коде

```python
from core.engine.eng import Engine

engine = Engine()
engine.initialize()
scene = engine.new_scene("MyScene")  # adds "Shadow System" entity, notifies plugins
engine.start_play()
# ... Engine.tick() is driven by GameWorker while in play mode ...
engine.stop_play()
```

## Сериализация

Неприменимо. Раскладка редактора не входит в формат сцены.

## Скриншоты

![Главное окно редактора ZarinEngine](../../assets/screenshots/editor-main.png)

> Главное окно: вьюпорт, Hierarchy, Inspector, панель Project, Console.
> Файл: `assets/screenshots/editor-main.png` (проверенная копия
> `zarin_screenshot.png` из репозитория движка).

## Связанные страницы

- [Быстрый старт](./quick-start.md)
- [Первая сцена](./first-scene.md)
- [Сущность Entity](../core/entity.md)
- [Логирование](../core/logging.md)
- [Профайлер](../core/profiler.md)
- [Главное окно](../editor/main-window.md)

## Проверено по

- `main.py` (формат, имя приложения, шаги сплэша, папки плагинов, `EditorMainWindow`, `engine.viewport`, геометрия, `IpcServer`)
- `README.md` (раздел Visual Editor)
- `core/engine/eng/engine.py` (`new_scene`, `_add_default_scene_objects`, `start_play`, `stop_play`)
- `editor/panels/` (листинги папок: hierarchy, project, console, terminal, profiler, timetravel, scripts_panel, script_editor_panel, undo_history_panel, collab, anim, terrain, prefab_editor_panel, plugin_manager_panel)
- `core/foundation/logger.py`, `core/foundation/profiler.py`, `core/foundation/cmd/history.py` (источники данных панелей)
