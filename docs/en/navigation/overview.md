# Navigation and NavAgent

Grid-based navigation (`NavWorld`) with a GPU-deferred path request; `NavAgent` steers the entity toward a target.

## Properties / API

| Field | Default | Meaning |
|---|---|---|
| `agent_radius / agent_height / agent_padding` | 0.5 / 2.0 / 0.0 | Agent capsule and wall padding |
| `max_climb / max_slope` | 0.5 / 45.0 | Walkable step and slope |
| `max_speed / max_angular_speed` | 5.0 / 360.0 | Steering limits |
| `use_physical_controller / flying / auto_move / show_path_cells` | true / false / false / false | Physics drive, fly mode, auto path, debug draw |
| `target_x / target_y / target_z / target_entity_id` | 0 / 0 / 0 / "" | Destination point or followed entity |
| `repath_interval / arrival_distance` | 1.0 / 0.5 | Replan period and stop radius |
| `nav_resolution / nav_world_size` | 512 / 500.0 | Grid density and extent |
| `set_target(pos) / set_target_entity(e) / stop() / has_path() / current_path` | — | Runtime API; `set_target` rebuilds grid and issues `find_path_gpu_deferred` |

## Usage in Editor

1. Add `NavAgent` to a character entity; tune radius/height/speed.
2. Set target XYZ or `target_entity_id`, enable `auto_move`; or press Inspector `Set Target` / `Stop` buttons.
3. Enable `show_path_cells` to debug the grid path in the viewport.

## Usage in Code

```python
from core.maths.math3d import Vec3

agent = entity.get_component_by_name("NavAgent")
agent.set_target(Vec3(10.0, 0.0, 5.0))
# or follow another entity:
agent.set_target_entity(player_entity)
if not agent.has_path():
    agent.stop()
```

## Related

- Terrain, Constraints (`MoveTowardsConstraint`), Physics character controller

## Verified against

- `core/components/navigation/navagent/component.py`
- `core/components/navigation/navagent/path.py`, `navagent/steering.py`
- `core/navigation/world.py`, `grid.py`, `path.py`
