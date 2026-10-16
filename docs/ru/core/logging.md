# Логирование

> `Logger` — статический лог с фильтром по уровню, in-memory кольцевым буфером (2000 записей) и колбэками-лисенерами, которые питают панель Console редактора.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Уровни — `LogLevel.DEBUG = 0, INFO = 1, WARNING = 2, ERROR = 3`; `_min_level` по умолчанию `DEBUG`, т.е. записывается всё (`debug`/`info`/`warning` отсекаются гейтом; `error()` пишет всегда). `_emit` добавляет `LogEntry(level, message, traceback_str)` (метка времени через `time.time()`), за пределом `_max_entries = LOGGER_MAX_ENTRIES = 2000` старейшая запись вытесняется, затем вызываются лисенеры (исключения лисенеров глотаются). `Logger.error(msg, exc)` прикладывает `traceback.format_exc()` только если `exc` передан. `main.py`/`player.py` направляют необработанные исключения через `Logger.error` с полным трейсбэком.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| LogLevel | IntEnum | `DEBUG/INFO/WARNING/ERROR = 0/1/2/3` | Порядок серьёзности |
| debug / info / warning | methods | `Logger.info(msg)` | Отсекаются через `_min_level` |
| error(msg, exc=None) | method | `exc=None` | `traceback.format_exc()` прикладывается, когда `exc` дан |
| add_listener(fn) / remove_listener(fn) | methods | — | `fn(LogEntry)`; сюда подписана панель консоли |
| get_entries | method | copy of list | Снапшот для отрисовки UI |
| clear | method | — | Сбрасывает все записи |
| _max_entries | int | `2000` | `LOGGER_MAX_ENTRIES` (`core/config/constants.py`) |
| LogEntry fields | slots | — | `level`, `message`, `timestamp`, `traceback_str`; хелперы `formatted_time()` (`%H:%M:%S`), `level_str()` |

## Использование в редакторе

Панель Console рендерит `get_entries()` (настройки шрифта — глобальный конфиг `console.*`). Ошибки движка, плагинов и скриптов появляются здесь с контекстом сущности/компонента.

## Использование в коде

```python
from core.foundation.logger import Logger

Logger.info("Play mode started.")
Logger.warning("Missing texture, using fallback.")
try:
    risky()
except Exception as e:
    Logger.error(f"System failed: {e}", e)

Logger.add_listener(lambda entry: print(entry.level_str(), entry.formatted_time(), entry.message))
```

```python
print(len(Logger.get_entries()))  # в кольцевом буфере не больше 2000 записей
Logger.clear()  # сбросить все записи (только рантайм, не сериализуется)
```

## Сериализация

Неприменимо. Записи только рантайм; `player.py` дополнительно зеркалит стартовую диагностику в `player_diag.txt`.

## Связанные страницы

- [Конфигурация](./config.md)
- [Профайлер](./profiler.md)
- [Обзор ECS](./ecs-overview.md)

## Проверено по

- `core/foundation/logger.py` (уровни, буфер, лисенеры, поведение трейсбэка в `error`)
- `core/config/constants.py` (`LOGGER_MAX_ENTRIES = 2000`)
- `main.py` (`excepthook` → `Logger.error`), `player.py` (`excepthook`, `player_diag.txt`)
- `core/ecs/query.py` (формат логирования ошибок покомпонентно)
