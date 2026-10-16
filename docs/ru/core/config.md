# Конфигурация

> `Config` — JSON-настройки с dot-нотацией (`config.get("physics.gravity_y")`), сохранением дефолтов и лисенерами изменений. Глобальный файл: `~/.zarin/settings.json`; проектный: `<project>/ProjectSettings.json`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`Config(path, defaults)` грузит JSON поверх defaults-словаря (глубокий мерж). `get` возвращает дефолт вызывающего, если хоть один сегмент пути отсутствует; `set` создаёт промежуточные словари и нотифицирует `on_changed`-лисенеров, если `notify` не `False`. `reset(key)` возвращает один ключ к дефолту (или весь файл при `key=None`). Ключи, требующие рестарта, помечены через `mark_restart` (`editor.theme`, `editor.font_size`, `editor.ui_scale`, `editor.language`, `engine.python_jit`, `engine.python_optimize`, `engine.python_unbuffered`, `engine.python_no_bytecode`). `main.py` читает `engine.python_jit / python_optimize / python_unbuffered / python_no_bytecode` до старта и перевыполняет интерпретатор при расхождении.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| get_global_config | function | singleton | Дефолты: `editor`, `camera`, `profiler`, `rendering`, `gizmo`, `console`, `terminal`, `viewport`, `collab`, `undo`, `engine`, `hierarchy`, `inspector`, `project`, `plugins`, `audio`, `file_assoc`, `mesh_preview` |
| get_project_config | function | per path | Дефолты: `project` (name, version, `default_scene=""`), `input`, `rendering`, `physics`, `audio` |
| engine.time_scale | float | `1.0` | Множитель логического времени |
| engine.fixed_update_dt | float | `0.02` | Фиксированный шаг (`Engine` клампит к `>= 0.001`) |
| rendering.vsync | bool | `True` | Swap interval в `main.py`/`player.py` |
| rendering.tick_rate | float | `120.0` | Частота апдейтов `GameWorker` |
| rendering.fixed_tick_rate | float | `60.0` | Частота fixed-шагов `GameWorker` |
| profiler.enabled | bool | `False` | Включает `engine.profiler` |
| physics.gravity_y | float | `-9.81` | Дефолт проекта; `gravity_x/z = 0.0` |
| project.default_scene | str | `""` | Фолбэк плеера №3 |
| get / set / has / reset / to_dict / update / save | methods | — | Полный API `Config` |
| on_changed(callback) | method | — | `callback(key, value)` на каждый нотифицированный `set` |

## Использование в редакторе

Диалог настроек правит глобальный конфиг и сохраняет в `~/.zarin/settings.json`. Ключи с пометкой рестарта (`theme`, `ui_scale`, `language`, `python_*`) применяются при следующем запуске. Настройки проекта (`ProjectSettings.json`) держат имя, версию, сцену по умолчанию, биндинги ввода, параметры физсолвера.

## Использование в коде

```python
from core.config.config import get_global_config, get_project_config

cfg = get_global_config()
dt = cfg.get("engine.fixed_update_dt", 0.02)
cfg.set("engine.time_scale", 0.5)
cfg.on_changed(lambda key, value: print(key, value))
cfg.save()

proj = get_project_config("/path/to/project")
print(proj.get("project.default_scene", ""))
```

```python
print(cfg.is_restart_key("editor.theme"))  # True: применится при следующем запуске
cfg.reset("engine.time_scale")  # вернуть один ключ к дефолту
```

## Сериализация

Файлы и есть формат: pretty-printed JSON (`json.dump(..., indent=2)`). Пример:

```json
{ "engine": { "time_scale": 1.0, "fixed_update_dt": 0.02 },
  "rendering": { "vsync": true, "tick_rate": 120.0 } }
```

## Связанные страницы

- [Время и Fixed Update](./time.md)
- [Логирование](./logging.md)
- [Профайлер](./profiler.md)
- [Первая сцена](../getting-started/first-scene.md)

## Проверено по

- `core/config/config.py` (API `Config`, дефолты `get_global_config`, дефолты `get_project_config`, пути, restart-ключи)
- `main.py` (`_apply_python_optimization`, `rendering.vsync`)
- `player.py` (`rendering.vsync`)
- `core/engine/eng/engine.py` (`initialize` читает `engine.time_scale`, `engine.fixed_update_dt`, `profiler.enabled`)
