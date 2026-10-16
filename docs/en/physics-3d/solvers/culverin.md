# Culverin Solver

Pure-Python fallback solver backend. Select it with `"solver": "culverin"` in the physics config; requires `pip install culverin`.

## Capabilities

| Key | Default | Notes |
|---|---|---|
| `culverin_num_threads` | `0` | `0` forces `max_physics_jobs = 0` and `max_physics_barriers = 0` (`solver.py:74-76`) |
| `culverin_max_physics_jobs` | `0` | Passed as `max_physics_jobs` to `PhysicsWorld` |
| `culverin_max_physics_barriers` | `0` | Passed as `max_physics_barriers` to `PhysicsWorld` |
| `culverin_max_bodies` | `65536` | Passed as `max_bodies` |
| `culverin_max_pairs` | `65536` | Passed as `max_pairs` |
| `culverin_max_contact_constraints` | `65536` | Passed as `max_contact_constraints` |
| `culverin_temp_allocator_size` | `16777216` | Passed as `temp_allocator_size` (16 MiB) |
| `culverin_penetration_slop` | `0.02` | Passed as `penetration_slop` |
| `culverin_enable_ccd` | `False` | Stored flag; CCD off by default |
| `culverin_enable_sleeping` | `True` | Stored flag; sleeping on by default |
| `gravity_x/y/z` | `0.0 / -9.81 / 0.0` | Applied via `PhysicsWorld.set_gravity` |

## Keys read vs ignored

Read in `initialize` (`solver.py:52-90`): `gravity_x`, `gravity_y`, `gravity_z`, `culverin_num_threads`, `culverin_max_physics_jobs`, `culverin_max_physics_barriers`, `culverin_penetration_slop`, `culverin_max_bodies`, `culverin_max_pairs`, `culverin_max_contact_constraints`, `culverin_temp_allocator_size`, `culverin_enable_ccd`, `culverin_enable_sleeping`.

NOT read: `num_sub_steps`, `solver_iterations`, `erp`. `step_simulation(dt)` forwards a single `world.step(dt)` (`solver.py:133-135`) — there is no sub-step loop.

## Usage in Editor

Set physics `solver` to `culverin` in the project config. Tune only the `culverin_*` keys above; changing `num_sub_steps` / `solver_iterations` / `erp` has no effect on this backend.

## Usage in Code

```python
from physics_solvers.culverin_solver.solverp.solver import CulverinSolver

solver = CulverinSolver()
ok = solver.initialize({
    "gravity_y": -9.81,
    "culverin_num_threads": 0,          # 0 => jobs/barriers forced to 0
    "culverin_max_bodies": 65536,
    "culverin_max_pairs": 65536,
    "culverin_max_contact_constraints": 65536,
    "culverin_temp_allocator_size": 16777216,
    "culverin_penetration_slop": 0.02,
    "culverin_enable_ccd": False,
    "culverin_enable_sleeping": True,
})
assert ok
solver.step_simulation(0.02)
```

## Fixed update notes

- `dt` is `Engine._fixed_dt` (`0.02` default); solving runs inside `solver.step_simulation(dt)`.
- `ray_cast` returns `body_id / position / normal=(0,0,0) / fraction`; `get_collision_events` returns `body_a / body_b` pairs only (no force).

## Related

Solver & Threading, Rigidbody, Collision Layers, PyBullet Solver, PhysX Solver.

## Verified against

- `physics_solvers/culverin_solver/solverp/solver.py:40-90` (`initialize`, defaults and key names)
- `config/config.py:352-379` (matching config defaults)
