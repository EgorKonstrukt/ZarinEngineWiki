# Solver & Threading

Where simulation actually runs, and how data crosses the thread/process boundary without gameplay code ever blocking.

## Properties

| Setting | Default | Where |
|---|---|---|
| `simulation_mode` | `multi_threaded` | `single` / `multi_threaded` / `per_layer_process` |
| `solver` | `culverin` | `culverin` (Jolt) / `pybullet` (Bullet) / `physx` (NVIDIA) |
| `fixed_time_step` | `0.02` | Step `dt` |
| `num_sub_steps` | `2` | Project config (PyBullet standalone default `1`; Culverin/Jolt ignores it) |
| `solver_iterations` | `10` | Project config (PyBullet standalone default `50`; Culverin/Jolt ignores it) |
| `erp` / `contact_erp` / `friction_erp` | `0.2` / `0.2` / `0.0` | Project config (PyBullet standalone `erp`/`contact_erp` default `0.4`; Culverin/Jolt ignores them) |
| `culverin_max_bodies` / `max_pairs` / `max_contact_constraints` | `65536` each | Jolt world caps |
| `culverin_temp_allocator_size` | `16777216` | 16 MB |
| `culverin_num_threads` / `max_jobs` / `max_barriers` | `0` / `0` / `0` | `0` = Jolt single-threaded |
| `culverin_penetration_slop` | `0.02` | |
| `culverin_enable_ccd` / `enable_sleeping` | `False` / `True` | |
| Shared flags | `1` active, `2` dirty, `4` kinematic/teleport, `8` 2D | `SharedPhysicsBuffer` |
| Limits | `65536` entities, `16` soft bodies × `32768` verts | `shared_buffer.py`, `soft_shared_buffer.py` |

## Usage in Editor

Prototype in default `multi_threaded`. Switch `simulation_mode` to `single` when using `CharacterController`/`Motor` or debugging solver behavior; `per_layer_process` for arenas that must never interact.

## Usage in Code

```python
# Gravity at runtime (IPhysicsSolver API):
solver.set_gravity((0.0, -9.81, 0.0))
solver.step_simulation(0.02)
solver.apply_force(body_id, (0.0, 10.0, 0.0))
solver.apply_torque(body_id, (0.0, 1.0, 0.0))
solver.apply_impulse(body_id, (0.0, 5.0, 0.0))
hit = solver.ray_cast((0.0, 5.0, 0.0), (0.0, -1.0, 0.0), 100.0)
```

Background step order (verified): coalesce pending `step` commands into one `dt` → apply teleports/forces from shared memory → `step_simulation(dt)` → zero 2D out-of-plane velocity → write back with version bump (+ soft buffers) → map body ids to entity ids for collision events. In-process path mirrors it without serialization: register → shape-check (every 60 steps) → ECS→physics → soft separation (only when `≥2` soft bodies) → step → constrain 2D → physics→ECS → soft sync → events.

## Fixed update notes

- `Engine.tick_fixed_step`: accumulate against `_fixed_dt` (`0.02`), cap backlog `5 * _fixed_dt`, max `5` steps/tick, then `scene.fixed_update(fd)` (component `on_fixed_update`) and finally system-plugin `step(fd)`.
- `PhysicsWorker` (threaded fallback): daemon `threading.Thread`, command poll timeout `0.004` s. `SceneThreadPool`: `max_workers = 2`, `ThreadPoolExecutor` named `phys-scene`.
- ECS→physics pushes only on dirty Transform or accumulated force; physics→ECS skips kinematic/dirty bodies; force accumulators clear after every push — call `add_force` each fixed step.

## Related

Physics Overview, Rigidbody, Collision Layers, CharacterController, Motor.

## Verified against

- `core/physics/proc/process.py` + `proc/sync.py` (`_process_step_shared`)
- `core/physics/physics_worker.py`
- `core/physics/scene/scene.py` + `scene/threadpool.py`
- `core/physics/shared_buffer.py`, `soft_shared_buffer.py`
- `core/config/config.py`, `core/engine/eng/loop.py`, `plugins/physics_plug/plugin.py`
- `physics_solvers/registry.py`, `culverin_solver/solverp/solver.py`, `pybullet_solver/solverp/solver.py`
