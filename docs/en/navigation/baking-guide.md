# NavAgent Guide & Navmesh Baking

Point-and-go navigation on a baked grid. `NavAgent` steers the entity along paths from `NavWorld`; the grid is rebuilt from scene colliders and cached per agent parameters.

## Properties

| Field | Default | Notes |
|---|---|---|
| `agent_radius` / `agent_height` / `agent_padding` | `0.5` / `2.0` / `0.0` | Wall clearance; `padding or radius` dilates obstacles when flying |
| `max_climb` / `max_slope` | `0.5` / `45.0` | Step height / walkable angle |
| `max_speed` / `max_angular_speed` | `5.0` / `360.0` | Steering limits |
| `use_physical_controller` / `flying` / `auto_move` | `True` / `False` / `False` | `on_start` requests a path only if `auto_move` |
| `target_x/y/z` / `target_entity_id` | `0/0/0` / `""` | `set_target_entity` follows the entity's position snapshot |
| `repath_interval` | `1.0` | s; backoff `×min(4, 2^min(fail_streak,2))` under failures |
| `arrival_distance` | `0.5` | Waypoint accepted within this distance; then advance |
| `nav_resolution` | `512` | Grid cells per side |
| `nav_world_size` | `500.0` | World units covered |

## Baking (grid APIs)

There is no `generate`/`build` method on `NavAgent` itself — baking = rebuilding the shared `NavWorld` grid:

- `NavWorld.rebuild_grid()` (`rebuild4.py:201`) — entry point used by `set_target` before `find_path_gpu_deferred`.
- `build_ground_obstacle_grid(agent_height_cells, max_climb_cells, …)` (`grid.py:216`) — rasterizes walkable/obstacle cells.
- `find_path_gpu_deferred(start, end, radius, height, flying, climb, slope, padding)` (`world.py:71`) — queues a job (cap 16 pending), returns `req_id`; the agent polls it via `_poll_path_result()`.
- `has_los(a, b, flying)` — steering shortcut: skip to the furthest visible waypoint; +3 blocked frames forces a repath.

Physically-driven agents (`use_physical_controller`) are driven through the character controller (`_set_cc_drive`); otherwise the transform is moved directly.

## Usage in Code

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
nav.set_target(points[0])  # enables auto_move + rebuilds grid + queues path

def on_update(self, dt):
    n = self._entity.get_component(NavAgent)
    if not n.has_path() and n._path_index == 0 and not n.auto_move:
        state["i"] = (state["i"] + 1) % len(points)
        n.set_target(points[state["i"]])
    # nav.stop() cancels: clears path, timers, and controller drive
```

## Related

Navigation Overview, Character Controller.

## Verified against

- `core/components/navigation/navagent/component.py:57-135` (fields/defaults, `set_target`, `set_target_entity`, `stop`)
- `core/components/navigation/navagent/steering.py:126-180` (repath backoff, `arrival_distance`, LOS skip)
- `core/navigation/world.py:71-120` (`find_path_gpu_deferred`)
- `core/navigation/rebuild4.py:201` (`rebuild_grid`), `core/navigation/grid.py:216` (`build_ground_obstacle_grid`)
