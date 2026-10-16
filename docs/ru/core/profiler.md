# Профайлер

> `Profiler` (`core/foundation/profiler.py`) пишет вложенные тайминг-секции в плоские покадровые данные плюс опциональный кольцевой буфер на 300 кадров. По умолчанию выключен; тогл — `profiler.enabled` или `engine.profiler_enabled`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Пока `enabled` равен `False`, все методы — no-op. `start(key)` пушит `(key, perf_counter)`; `stop(key)` снимает совпавшую запись (nesting-safe: ищет по стеку при несовпадении вершины) и аккумулирует миллисекунды в `_flat_data`. `scope(key)` — форма контекстного менеджера. `set_value(key, ms)` пишет абсолютное значение (используется для `scene_serialize`). `capture_frame()` запечатывает текущий кадр (время кадра, сэмплы с глубиной + цветом из `_PROFILER_COLORS`) в `deque(maxlen=300)`. `Engine` выставляет `profiler`, `profiler_data`, `get_profiler_data(key, default)`, `set_profiler_data`, `capture_profiler_frame()`, `reset_profiler()` и свойство `profiler_enabled`. Ключи движка: `tick`, `fixed_update`, `update`, `scene_update`, `scene_fixed_update`, `scene_start`, `scene_serialize`, плюс секции `NAME` плагинов и ключи подсистем (`physics_ms`, `render_scene`, `audio_ms`, `ui_ms`, `particle_ms`, `animation_ms`, `gizmo_lines`, `frame_overhead`, …).

## Свойства

| name | type | default | описание |
|---|---|---|---|
| enabled | bool | `False` | Мастер-выключатель; переключение сбрасывает стек/состояние кадра |
| capture_frames | bool | `False` | При `True` покадровые сэмплы сохраняются |
| max_frames | int | `300` | Аргумент конструктора `deque(maxlen=...)` |
| start(key) / stop(key) | methods | — | `stop` возвращает длительность в мс или `None` |
| scope(key) | context manager | — | `with profiler.scope("my_sys"):` |
| set_value(key, ms) | method | — | Абсолютная запись (не аккумуляция) |
| capture_frame | method | — | Запечатать кадр в кольцевой буфер |
| reset | method | — | Чистит кадры, стек, плоские данные, счётчики |
| data | dict[str, float] | `{}` copy | Плоские накопленные тайминги (мс) |
| frames | list[FrameProfile] | copy | Запечатанные кадры с `samples`, `frame_time_ms`, `flat_data` |
| engine surface | properties | — | `engine.profiler`, `engine.profiler_data`, `engine.profiler_enabled` |

## Использование в редакторе

Панель профайлера читает `engine.profiler_data` с периодом `profiler.refresh_interval` (дефолт 500 мс) и буфер кадров для графиков. Включается через глобальный конфиг `profiler.enabled` или в рантайме.

## Использование в коде

```python
engine.profiler_enabled = True
with engine.profiler.scope("my_system"):
    step_simulation(dt)
engine.set_profiler_data("my_metric", 1.25)
print(engine.get_profiler_data("tick", 0.0))
engine.capture_profiler_frame()
```

## Сериализация

Неприменимо. Данные профайлера только рантайм.

## Связанные страницы

- [Время и Fixed Update](./time.md)
- [Логирование](./logging.md)
- [Конфигурация](./config.md)

## Проверено по

- `core/foundation/profiler.py` (`Profiler`, `ProfileSample`, `FrameProfile`, `_PROFILER_COLORS`, `max_frames=300`)
- `core/engine/eng/loop.py` (методы `tick_begin`/`tick_fixed_step`/`tick_update`; ключи `tick`/`fixed_update`/`update`, поверхность свойств `profiler`)
- `core/config/config.py` (`profiler.enabled`, `profiler.refresh_interval`)
- `core/ecs/query.py` (скоупы `scene_update`, `scene_fixed_update`, `scene_start`)
- `core/ecs/scene_serialize.py` (`profiler.set_value("scene_serialize", 0)`)
