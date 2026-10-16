# Time Travel

> `SnapshotRecorder(max_frames=600, capture_interval=1)` пишет полные сериализации сцены в кольцевой буфер и восстанавливает любой кадр через `Scene.deserialize`. От стека undo команд не зависит.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`FrameSnapshot` хранит один кадр: `frame_number`, `timestamp`, `entity_count`, `data` (полный dict сцены), `frame_time_ms`, плюс опциональные `camera_data`, `bookmarked`, `breakpoint_hit` и md5-`hash` данных (`_compute_hash` по JSON с сортированными ключами). Модульные хелперы сравнивают снапшоты: `diff_scenes(old, new)` отдаёт `created` / `deleted` / `changed` id сущностей с подетальными дельтами `(field, old, new)` по компонентам (пропуская `_key`/`type`), а `find_entity_frame_changes(frames, entity_id)` — индексы кадров, где менялись данные одной сущности.

`SnapshotRecorder(max_frames=600)` держит `deque(maxlen=max_frames)`:

- `start()` начинает запись (сбрасывает счётчики); `stop()` заканчивает; `clear()` опустошает буфер.
- `capture(scene, frame_time_ms=0.0, camera_data=None)` сериализует сцену каждый `capture_interval`-й вызов (дефолт `1`, сеттер клампит к ≥ 1). При `filter_unchanged` кадры с совпавшим хешем пропускаются. Опциональный `breakpoint_expr` вычисляется через `eval` с `scene_data`, `entity_count`, `frame`, `time` (плюс extras из `set_breakpoint_ns`); truthy-результат помечает кадр и останавливает запись.
- `restore(registry, index)` десериализует снапшот в новую `Scene` и вызывает `mark_dirty()`; при ошибке возвращает `None` (с логом).
- Закладки: `toggle_bookmark(index)`, `bookmarked_indices()`, `clear_bookmarks()`. `export_frame(index, path)` пишет один снапшот в JSON. `frames()` / `get_frame(index)` читают буфер; `num_frames`, `total_frames`, `is_recording`, `max_frames`, `capture_interval` — проперти.

Связь с undo: стек команд (`CommandHistory`) пишет дискретные правки и генерит `history_changed`; рекордер хранит целые состояния сцены (обычно во время Play), а редактор при восстановлении подменяет сцену и генерит `scene_loaded` — общих данных у них нет.

## Свойства

| имя | тип | дефолт | описание |
|---|---|---|---|
| `SnapshotRecorder(max_frames)` | класс | `600` | Кольцевой буфер `FrameSnapshot` |
| `capture_interval` | int | `1` | Писать каждый N-й вызов `capture()` |
| `filter_unchanged` | bool | `False` | Пропускать кадры с md5 как у прошлого |
| `breakpoint_expr` | str | `""` | Условие паузы записи по данным сцены |
| `capture(scene, ...)` | метод | — | Сериализовать сцену; возвращает индекс в буфере или `None` |
| `restore(registry, index)` | метод | — | `Scene.deserialize` + `mark_dirty()` |
| `toggle_bookmark` / `bookmarked_indices` | методы | — | Пометить / перечислить кадры-закладки |
| `export_frame(index, path)` | метод | `False` при ошибке | Сбросить один кадр в JSON |
| `diff_scenes` / `find_entity_frame_changes` | функции | — | Хелперы сравнения снапшотов |

## Использование в редакторе

Панель time-travel дёргает `start` / `stop` / `capture` / `restore` / `toggle_bookmark` / `export_frame`; после восстановления генерит `scene_loaded`, чтобы вьюпорт, иерархия и инспектор перепривязались (`editor/panels/timetravel/playback.py`).

## Использование в коде

```python
from core.foundation.time_travel import SnapshotRecorder, diff_scenes
from core.ecs import ComponentRegistry

rec = SnapshotRecorder(max_frames=600)
rec.capture_interval = 2
rec.filter_unchanged = True
rec.start()
idx = rec.capture(scene, frame_time_ms=16.6)
rec.stop()

restored = rec.restore(ComponentRegistry, 0)
print(diff_scenes(rec.get_frame(0).data, rec.get_frame(-1).data).keys())
rec.toggle_bookmark(0)
rec.export_frame(0, "frame_0.json")
```

## Сериализация

Снапшоты — полные dict сцены (та же схема, что `.zpes`); `export_frame` пишет один как есть в JSON. Восстановление прогоняет обычный `Scene.deserialize`, поэтому неизвестные типы компонентов пропускаются ровно как при загрузке файла.

## Связанные страницы

- [Foundation: Curve, команды, Time Travel](../foundation.md)
- [Команды и Undo](./commands-undo.md)
- [События движка](./engine-events.md)
- [Сцены (.zpes)](../../scenes-prefabs/scenes.md)

## Проверено по

- `core/foundation/time_travel.py` (`FrameSnapshot`, `_compute_hash`, `diff_scenes`, `_diff_entity`, `_diff_component`, `find_entity_frame_changes`, `SnapshotRecorder`)
- `editor/panels/timetravel/playback.py` (генерация `scene_loaded` после restore)
- `editor/main_window/handlersp/play.py` (воркер-тред снапшотов)
