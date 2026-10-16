# NavAgent и запекание навмеша

Навигация «указал-пошёл» по запечённой сетке. `NavAgent` ведёт сущность вдоль путей `NavWorld`; сетка перестраивается из коллайдеров сцены и кэшируется под параметры агента.

## Свойства

| Поле | Дефолт | Примечания |
|---|---|---|
| `agent_radius` / `agent_height` / `agent_padding` | `0.5` / `2.0` / `0.0` | Зазор до стен; `padding or radius` раздувает препятствия в полёте |
| `max_climb` / `max_slope` | `0.5` / `45.0` | Высота ступени / проходимый угол |
| `max_speed` / `max_angular_speed` | `5.0` / `360.0` | Лимиты руления |
| `use_physical_controller` / `flying` / `auto_move` | `True` / `False` / `False` | `on_start` запрашивает путь, только если `auto_move` |
| `target_x/y/z` / `target_entity_id` | `0/0/0` / `""` | `set_target_entity` берёт снапшот позиции сущности |
| `repath_interval` | `1.0` | с; бэкофф `×min(4, 2^min(fail_streak,2))` при неудачах |
| `arrival_distance` | `0.5` | Вейпоинт засчитан в этом радиусе; далее следующий |
| `nav_resolution` | `512` | Клеток на сторону |
| `nav_world_size` | `500.0` | Покрытие в мировых единицах |

## Запекание (API сетки)

Методов `generate`/`build` у самого `NavAgent` нет — запекание = перестроение общей сетки `NavWorld`:

- `NavWorld.rebuild_grid()` (`rebuild4.py:201`) — точка входа; `set_target` вызывает её перед `find_path_gpu_deferred`.
- `build_ground_obstacle_grid(agent_height_cells, max_climb_cells, …)` (`grid.py:216`) — растеризует проходимые/препятствия.
- `find_path_gpu_deferred(start, end, radius, height, flying, climb, slope, padding)` (`world.py:71`) — ставит задачу (лимит 16), возвращает `req_id`; агент забирает результат через `_poll_path_result()`.
- `has_los(a, b, flying)` — срезка руления: прыжок к дальней видимой точке; +3 заблокированных кадра форсируют репат.

Агенты с `use_physical_controller` едут через character controller (`_set_cc_drive`), иначе двигается напрямую трансформ.

## Использование в коде

```python
scene = Engine.instance().scene
bot = scene.create_entity("PatrolBot")
from core.components.navigation.navagent.component import NavAgent
from core.maths.math3d import Vec3

nav = NavAgent()
nav.max_speed = 4.0
nav.arrival_distance = 0.5
nav.repath_interval = 1.0
nav.nav_resolution = 512
nav.nav_world_size = 500.0
bot.add_component(nav)

points = [Vec3(10, 0, 0), Vec3(10, 0, 10), Vec3(0, 0, 10), Vec3(0, 0, 0)]
state = {"i": 0}
nav.set_target(points[0])  # включает auto_move + перестраивает сетку + ставит путь

def on_update(self, dt):
    n = self._entity.get_component(NavAgent)
    if not n.has_path() and n._path_index == 0 and not n.auto_move:
        state["i"] = (state["i"] + 1) % len(points)
        n.set_target(points[state["i"]])
    # nav.stop() отменяет: чистит путь, таймеры и привод контроллера
```

## Связанные

Navigation Overview, Character Controller.

## Проверено по

- `core/components/navigation/navagent/component.py:57-135` (поля/дефолты, `set_target`, `set_target_entity`, `stop`)
- `core/components/navigation/navagent/steering.py:126-180` (бэкофф репата, `arrival_distance`, LOS-срезка)
- `core/navigation/world.py:71-120` (`find_path_gpu_deferred`)
- `core/navigation/rebuild4.py:201` (`rebuild_grid`), `core/navigation/grid.py:216` (`build_ground_obstacle_grid`)
