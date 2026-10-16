# Logging

> `Logger` is a static level-filtered log with an in-memory ring buffer (2000 entries) and listener callbacks that feed the editor Console panel.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

Levels are `LogLevel.DEBUG = 0, INFO = 1, WARNING = 2, ERROR = 3`; `_min_level` defaults to `DEBUG` so everything is recorded (`debug`/`info`/`warning` are gated; `error()` always emits). `_emit` appends a `LogEntry(level, message, traceback_str)` (timestamped with `time.time()`), drops the oldest entry past `_max_entries = LOGGER_MAX_ENTRIES = 2000`, then calls listeners (listener exceptions are swallowed). `Logger.error(msg, exc)` captures `traceback.format_exc()` only when `exc` is passed. `main.py`/`player.py` route unhandled exceptions through `Logger.error` with a full traceback.

## Properties

| name | type | default | description |
|---|---|---|---|
| LogLevel | IntEnum | `DEBUG/INFO/WARNING/ERROR = 0/1/2/3` | Severity ordering |
| debug / info / warning | methods | `Logger.info(msg)` | Level-gated by `_min_level` |
| error(msg, exc=None) | method | `exc=None` | Appends `traceback.format_exc()` when `exc` given |
| add_listener(fn) / remove_listener(fn) | methods | — | `fn(LogEntry)`; console panel subscribes here |
| get_entries | method | copy of list | Snapshot for UI rendering |
| clear | method | — | Drops all entries |
| _max_entries | int | `2000` | `LOGGER_MAX_ENTRIES` (`core/config/constants.py`) |
| LogEntry fields | slots | — | `level`, `message`, `timestamp`, `traceback_str`; helpers `formatted_time()` (`%H:%M:%S`), `level_str()` |

## Usage in Editor

Console panel renders `get_entries()` (font settings under global config `console.*`). Engine, plugin, and script errors appear here with entity/component context.

## Usage in Code

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
print(len(Logger.get_entries()))  # ring buffer holds at most 2000 entries
Logger.clear()  # drop all entries (runtime-only, never serialized)
```

## Serialization

Not applicable. Entries are runtime-only; `player.py` additionally mirrors startup diagnostics to `player_diag.txt`.

## Related pages

- [Configuration](./config.md)
- [Profiler](./profiler.md)
- [ECS Overview](./ecs-overview.md)

## Verified against

- `core/foundation/logger.py` (levels, buffer, listeners, `error` traceback behavior)
- `core/config/constants.py` (`LOGGER_MAX_ENTRIES = 2000`)
- `main.py` (`excepthook` → `Logger.error`), `player.py` (`excepthook`, `player_diag.txt`)
- `core/ecs/query.py` (per-component error logging format)
