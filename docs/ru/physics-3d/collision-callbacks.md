# Колбэки столкновений

Три фазы из сравнения контактных сетов между кадрами: `on_collision_enter` (новые пары), `on_collision_stay` (сохранившиеся), `on_collision_exit` (пропавшие). Оба тела уведомляются с id *другой* сущности.

## Свойства

| Пункт | Значение |
|---|---|
| Фазы | `enter` / `stay` / `exit`: `entered = current − prev`, `exited = prev − current`, `stayed = current ∩ prev` (`events.py:305-307`) |
| Сигнатура скриптов | `other_id` ИЛИ `(other_id, force)` — арность по `inspect.signature` при загрузке |
| Сигнатура компонентов | Всегда `(other_id, force)` |
| `force` | `float(ev.get("force", 0.0))`, максимум по дубликатам событий пары |
| Гейт | `_has_collision_listeners()` пропускает весь проход, если слушателей нет |

## Правило арности (скрипты)

При загрузке скрипта (`hotreload.py:240-248`):

```python
nargs = len(inspect.signature(meth).parameters)  # при ошибке 1
self._py_collision_arity[cb] = 2 if nargs >= 2 else 1
```

Диспатч (`events.py:317-336`): арность `>= 2` → `callback(other_id, force)`, иначе `callback(other_id)`. Компоненты (`_invoke_component_collision`, `events.py:356-367`) всегда получают `(other_id, force)`, а инстансы `ScriptComponent` там пропускаются (их обрабатывает ветка скриптов).

## Использование в коде

```python
# скрипты: форма с 1 аргументом тоже работает — force передаётся, только если объявлен
def on_collision_enter(self, other_id):
    print("hit:", other_id)

def on_collision_stay(self, other_id, force=0.0):
    print("stay:", other_id, force)

def on_collision_exit(self, other_id):
    print("left:", other_id)
```

```python
# обычные компоненты обязаны принимать оба аргумента:
class DamageOnHit(Component):
    def on_collision_enter(self, other_id, force):
        ...
```

## Заметки fixed update

- События берутся из `solver.get_collision_events()` каждый физшаг; затем `prev_frame_contacts` заменяется текущим сетом (`events.py:348-354`).
- Слушатели перепроверяются при смене сигнатуры числа сущностей/компонентов (`_listener_signature`).

## Связанные

Collision Layers, Rigidbody, Solver & Threading, Script Component, Physics 2D Overview (те же колбэки).

## Проверено по

- `core/physics/scene/events.py:255-367` (проверка слушателей, diff сетов, оба диспатчера)
- `core/components/scripting/scriptc/hotreload.py:240-248` (арность через `inspect.signature`)
- `tests/smoke/test_scripting.py:233-248` (ожидания арности `1` vs `2`)
