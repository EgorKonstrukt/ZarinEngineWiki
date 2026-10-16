# Foundation: Curve, команды, Time Travel

> `core/foundation/` — общедвижковые утилиты: `Curve` (кейфреймовая float-функция), стек undo `Command`/`CommandHistory` и `SnapshotRecorder` (покадровые снапшоты сцены для time travel).

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

**Curve** (`core/foundation/curve.py`): `@dataclass Curve(keys, pre_wrap="clamp", post_wrap="clamp")`. `CurveKey(time, value, in_tangent=0.0, out_tangent=0.0, tangent_mode=SMOOTH)`. `TangentMode`: `FREE`, `LINEAR`, `CONSTANT`, `SMOOTH`. `add_key` вставляет с сортировкой и пересглаживает SMOOTH-касательные; `evaluate(t)` клампит вне диапазона ключей, внутри возвращает `CONSTANT`/`LINEAR`/Эрмит. `evaluate_array` использует Cython batch-путь при наличии. `to_list`/`from_list` хранят `[[time, value]]`; `to_dict`/`from_dict` сохраняют касательные и wrap-режимы. `QuaternionCurve` — slerp-вариант для вращений (`(0,0,0,1)` когда пуст).

**Команды** (`core/foundation/cmd/`): база `Command` (`execute`, `undo`, `redo` → повторный `execute`, `merge` → `False`, `description`). `CompoundCommand(commands, description)` выполняет/откатывает по порядку/в обратном порядке. `CommandHistory(max_size=100)`: `execute` (чистит redo), `undo`/`redo`, `seek(target_head)` для слайдера панели истории, `mark_saved`/`is_dirty`, `can_undo`/`can_redo`, `undo_text`/`redo_text`, `undo_count`/`redo_count`, списки описаний. `get_history()` возвращает провайдер или модульный синглтон. Готовые: `CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`. `execute` и `seek` всегда генерят событие движка `history_changed`; `undo`/`redo` — когда зарегистрированы лисенеры изменений (`add_on_change` / `set_on_undo` / `set_on_redo`).

**Time travel** (`core/foundation/time_travel.py`): `SnapshotRecorder(max_frames=600)` держит `FrameSnapshot(frame_number, timestamp, entity_count, data, frame_time_ms)` с md5-`hash`, опциональными `camera_data`, `bookmarked`, `breakpoint_hit`. `capture_interval` (дефолт `1`), `filter_unchanged`, `breakpoint_expr` (вычисляется с `scene_data`, `entity_count`, `frame`, `time`; truthy-результат останавливает запись). `capture(scene)` сериализует сцену; `restore(registry, index)` десериализует снапшот обратно в `Scene`. Хелперы: `diff_scenes`, `find_entity_frame_changes`, `toggle_bookmark`, `export_frame`.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| Curve.add_key(time, value) | method | — | Вставка с сортировкой + автосглаживание; возвращает `CurveKey` |
| Curve.evaluate(t) | method | `0.0` empty | Клампнутая интерполяция Эрмит/LINEAR/CONSTANT |
| TangentMode | Enum | `SMOOTH` | `free/linear/constant/smooth` |
| CommandHistory | class | `max_size=100` | Undo/redo/seek/dirty-трекинг |
| history_changed | event | — | Emit на `execute`/`seek` всегда; на `undo`/`redo` — когда есть лисенеры изменений |
| SnapshotRecorder | class | `max_frames=600` | Кольцевой буфер снапшотов сцены |
| SnapshotRecorder.capture_interval | int | `1` | Писать каждый N-й кадр |

## Использование в редакторе

Панель curve-редактора правит `Curve`-поля (тип `curve` в инспекторе). Панель undo-истории крутит `seek()`. Панель time-travel крутит `start/stop/capture/restore/toggle_bookmark/export_frame`.

## Использование в коде

```python
from core.foundation.curve import Curve
from core.foundation.cmd.history import get_history
from core.foundation.cmd.entity_cmds import CreateEntityCommand
from core.foundation.time_travel import SnapshotRecorder
from core.ecs import ComponentRegistry

curve = Curve()
curve.add_key(0.0, 0.0)
curve.add_key(1.0, 10.0)
print(curve.evaluate(0.5))

history = get_history()
history.execute(CreateEntityCommand(scene, "Enemy"))
history.undo()

rec = SnapshotRecorder()
rec.start()
rec.capture(scene)
restored = rec.restore(ComponentRegistry, 0)
```

## Сериализация

Кривые сериализуются как `to_dict()` (`keys` с `time/value/in_tangent/out_tangent/tangent_mode`, `pre_wrap`, `post_wrap`); `ScriptComponent` хранит их в `script_fields`. Снапшоты сериализуются как полные dict сцены (`export_frame` пишет один в JSON).

## Связанные страницы

- [Обзор ECS](./ecs-overview.md)
- [Компоненты и жизненный цикл](./components-lifecycle.md)
- [Реестр и сериализация](./registry-serialization.md)
- [Время и Fixed Update](./time.md)

## Проверено по

- `core/foundation/curve.py` (`Curve`, `CurveKey`, `TangentMode`, `QuaternionCurve`)
- `core/foundation/cmd/history.py` (`Command`, `CompoundCommand`, `CommandHistory`, `get_history`, `history_changed`)
- `core/foundation/cmd/entity_cmds.py` (`CreateEntityCommand`, `DeleteEntityCommand`, `InstantiatePrefabCommand`)
- `core/foundation/time_travel.py` (`SnapshotRecorder`, `FrameSnapshot`, `diff_scenes`, `restore`)
- `core/components/scripting/scriptc/component.py` (Curve в `script_fields`)
