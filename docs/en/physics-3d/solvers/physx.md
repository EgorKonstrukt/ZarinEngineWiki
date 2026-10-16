# PhysX Solver

USD/Omni (`ovphysx`) backend. Tensor-driven: poses, velocities, and wrenches flow through bound tables; gravity is baked into the generated scene.

## Properties

| Key | Default | Notes |
|---|---|---|
| `physx_device` / `device` | `"cpu"` | `opts.get("physx_device", opts.get("device", "cpu"))`; `PhysX(device=device)` |
| `gravity_x/y/z` | `0.0 / -9.81 / 0.0` | Stored; written into USD as `physics:gravityDirection` + `physics:gravityMagnitude` (`scenes.py`) |
| `debug_draw` | setter is a no-op (`pass`) | Reading works, enabling does nothing on this backend |

## What `initialize` reads

Only `physx_device`/`device` and `gravity_x/y/z` (`solver.py:57-71`). Honest limitation, verified by grep over the whole `physx_solver/` package:

- `num_sub_steps` — **not read** (no match in package).
- `solver_iterations` — **not read** (no match in package).
- `erp` — **not read** (no match in package).

`set_gravity` only stores the tuple and marks `_bodies_dirty = True`; it takes effect when the scene is (re)built. `step_simulation(dt)` accumulates `_sim_time += dt`, calls `ov_physx.step(dt, sim_time)` + `wait_all()`, then pulls transforms from the pose table.

## Usage in Code

```python
from physics_solvers.physx_solver.solverp.solver import PhysXSolver

solver = PhysXSolver()
ok = solver.initialize({
    "physx_device": "cpu",   # or "cuda"/"gpu" depending on ovphysx build
    "gravity_x": 0.0,
    "gravity_y": -9.81,
    "gravity_z": 0.0,
})
assert ok
solver.step_simulation(0.02)
```

Raycast returns the closest body by distance to `hit["position"]` with `body_id / position / normal / distance`. `get_collision_events` adapts `get_contact_report()` headers/points into `body_a / body_b / type / points[position, normal, impulse, separation]`.

## Fixed update notes

- `dt` is `Engine._fixed_dt` (`0.02` default); stepping runs inside `solver.step_simulation(dt)`.
- Pending velocity/wrench writes are flushed before each step (`_flush_pending`).

## Related

Culverin Solver, PyBullet Solver, Solver & Threading, Rigidbody, Collision Layers.

## Verified against

- `physics_solvers/physx_solver/solverp/solver.py:17-78` (`__init__` defaults, `initialize` key reads)
- `physics_solvers/physx_solver/solverp/solver.py:183-196` (`step_simulation`)
- `physics_solvers/physx_solver/solverp/scenes.py:54-81` (gravity direction/magnitude USD export)
- `physics_solvers/physx_solver/solverp/solver.py:272-311` (`get_collision_events` mapping)
