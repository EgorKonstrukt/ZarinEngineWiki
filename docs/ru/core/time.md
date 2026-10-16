# Время и Fixed Update

> Время кадра течёт через `Engine.tick()`: аккумулятор + фиксированные шаги (`fixed_dt`, дефолт `0.02`, максимум 5 шагов), затем один variable-`dt` апдейт, масштабированный `time_scale`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`Engine.tick()` — no-op, пока `play_mode` не равен `True`. `tick_begin()` флашит dirty-трансформы, меряет сырой `dt` через `perf_counter`, умножает на `time_scale` и копит в `_fixed_accum` (кламп к `5 * fixed_dt`). Затем до `MAX_FIXED_STEPS = 5` вызовов `tick_fixed_step()` выполняют `Scene.fixed_update(fd)` плюс системные `pre_step`/`step` плагинов, каждый съедает один `fixed_dt`. `tick_update(dt)` один раз выполняет `Scene.update(dt)` и обновляет счётчики FPS/TPS по немасштабированному времени (окно 0.5 с). `Engine.initialize()` читает `engine.time_scale` (дефолт `1.0`) и `engine.fixed_update_dt` (дефолт `0.02`, минимум `0.001`) из глобального конфига.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| time_scale | float | `1.0` | Сеттер клампит к `>= 0.0`; масштабирует `dt` |
| fixed_dt | float | `0.02` | Сеттер клампит к `>= 0.001` |
| MAX_FIXED_STEPS | int | `5` | Кап на `tick()` (локальная константа в `loop.py`, зеркало в `core/config/constants.py`) |
| fps / tps | float | `0.0` | Измеряемые счётчики, окно 0.5 с |
| frame_count | int | `0` | Инкремент за `tick_update` |
| tick() | method | — | No-op вне play-режима |
| on(event, cb) / off(event, cb) | methods | — | Шина событий (`EngineEventsMixin`) |

## Использование в редакторе

Кнопка Play → `start_play()` (сброс аккумулятора, `Scene.start()`, старт `GameWorker` с `rendering.tick_rate` 120 Гц / `rendering.fixed_tick_rate` 60 Гц). Стоп → `stop_play()` (стоп воркера и всего аудио). `time_scale` правится в настройках; `0` ставит на паузу логическое время, кадры идут.

## Использование в коде

```python
engine.time_scale = 0.5   # slow motion: dt halved
engine.fixed_dt = 1.0 / 60.0

def on_play(data):
    print("play started")

engine.on("play_start", on_play)
engine.off("play_start", on_play)
```

```python
class PhysicsReader:
    def on_fixed_update(self, dt: float):
        assert dt == engine.fixed_dt  # constant step
```

## Сериализация

Неприменимо. `time_scale` / `fixed_update_dt` хранятся в `~/.zarin/settings.json` через `Config`.

## Связанные страницы

- [Обзор ECS](./ecs-overview.md)
- [Компоненты и жизненный цикл](./components-lifecycle.md)
- [Конфигурация](./config.md)
- [Профайлер](./profiler.md)

## Проверено по

- `core/engine/eng/engine.py` (дефолты `__init__`, `initialize`, `start_play`, `stop_play`, `time_scale`/`fixed_dt` в loop-миксине)
- `core/engine/eng/loop.py` (`tick`, `tick_begin`, `tick_fixed_step`, `tick_update`, FPS/TPS)
- `core/engine/eng/events.py` (`on`, `off`, `_emit_event`)
- `core/config/constants.py` (`MAX_FIXED_STEPS = 5`)
- `core/config/config.py` (`engine.time_scale`, `engine.fixed_update_dt`, `rendering.tick_rate`, `rendering.fixed_tick_rate`)
