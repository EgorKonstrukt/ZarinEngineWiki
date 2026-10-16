# Physics 3D Overview

3D rigid-body simulation in ZarinEngine: entities with a `Rigidbody` plus one or more colliders are stepped by a pluggable solver (`IPhysicsSolver`), everything else is static scenery.

## Properties

| Setting | Default | Where |
|---|---|---|
| Solver | `culverin` (Jolt via `culverin` package) | `physics_solvers/registry.py` `DEFAULT_SOLVER`, `ProjectSettings.json` `physics.solver` |
| Gravity | `(0.0, -9.81, 0.0)` | `physics.gravity_x/y/z`, `CulverinSolver._gravity` |
| Fixed step | `0.02` s | `engine.fixed_update_dt`, `physics.fixed_time_step`, `Engine._fixed_dt` |
| Sub-steps | `2` | `physics.num_sub_steps` |
| Solver iterations | `10` | `physics.solver_iterations` |
| Simulation mode | `multi_threaded` | `physics.simulation_mode` (`single` / `multi_threaded` / `per_layer_process`) |

## Usage in Editor

1. Static floor: add `BoxCollider` only (no `Rigidbody`).
2. Dynamic crate: add `Rigidbody` (mass `1.0`) + `BoxCollider` (size `1,1,1`).
3. Press Play. Kinematic movers: tick `is_kinematic` and move the Transform; triggers: tick `is_trigger` and read overlap callbacks.

## Usage in Code

```python
entity = scene.create_entity("Crate")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.box_collider import BoxCollider
from core.maths.math3d import Vec3

rb = Rigidbody()
rb.mass = 1.0
entity.add_component(rb)
box = BoxCollider()
box.size = Vec3(1.0, 1.0, 1.0)
entity.add_component(box)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is not None:
        rb.add_force(Vec3(0.0, 10.0, 0.0))
        rb.add_torque(Vec3(0.0, 1.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass  # (other_entity_id, impact_force), also on_collision_stay/exit
```

Freeze axes: `rb.freeze_pos_x/y/z`, `rb.freeze_rot_x/y/z` (serialized as `freeze_pos` / `freeze_rot` triples).

## Fixed update notes

- `Engine.tick_fixed_step` (`core/engine/eng/loop.py`) accumulates real time against `_fixed_dt` (`0.02`), caps the backlog at `5 * _fixed_dt`, runs at most `5` steps per tick (`MAX_FIXED_STEPS`), then runs `scene.fixed_update(fd)` (component `on_fixed_update`: `Buoyancy`/`Motor`/`CharacterController` forces and drives) and finally system-plugin `step(fd)`.
- `PhysicsScene.step` order: register new entities → shape-change check (every 60 steps) → ECS→physics sync → soft-body AABB separation (only when `≥2` soft bodies) → `solver.step_simulation(dt)` → 2D constrain → physics→ECS sync → soft sync → collision events.
- Solver-param scoping: `num_sub_steps` (`2`), `solver_iterations` (`10`), `erp` (`0.2`) are project-config defaults consumed by the PyBullet solver (whose standalone defaults are `1` / `50` / `0.4`); the default Culverin/Jolt solver ignores them and uses the `culverin_*` world caps instead.
- Collision callbacks: script methods accept `(other_id)` or `(other_id, force)` — arity auto-detected via `inspect.signature` (`ScriptComponent._py_collision_arity`); engine-component handlers always receive `(other_id, force)`.
- Threading: `multi_threaded` runs the solver in a daemon `multiprocessing.Process` (`PhysicsProcess`) over `SharedPhysicsBuffer` / `SoftSharedBuffer`; `single` runs `PhysicsScene` in-process (required by `CharacterController` / `Motor`); `per_layer_process` spawns processes on demand per layer.

## Related

Rigidbody, Box/Sphere/Capsule/Mesh/Terrain/GSVolume colliders, CharacterController, Joints, Motor, Physics Materials, Collision Layers, Solver & Threading, Buoyancy, Soft Body, PhysBone, Constraints (Transform followers — a separate system, not solver joints).

## Verified against

- `core/physics/scene/scene.py` (`step`, `_register_new_entities`, `_constrain_2d_bodies`)
- `core/physics/scene/events.py` (`_process_collision_events`)
- `core/engine/eng/loop.py` (`tick_fixed_step`, `MAX_FIXED_STEPS = 5`)
- `core/engine/eng/engine.py` (`_fixed_dt = 0.02`)
- `core/config/config.py` (`physics.*` defaults)
- `physics_solvers/registry.py` (`DEFAULT_SOLVER = "culverin"`)
- `plugins/physics_plug/plugin.py` (simulation modes)
