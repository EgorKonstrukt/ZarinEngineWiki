# Rigidbody

Makes an entity simulated: mass, gravity response, accumulated forces, readable velocities. Without it a collider is static scenery; with it the entity joins the dynamics world.

## Properties

| Field | Default | Notes |
|---|---|---|
| `mass` | `1.0` | Inspector `0.001–100000`; forced to `0.0` at body creation when `is_kinematic` |
| `drag` | `0.0` | Inspector `0–1000` |
| `angular_drag` | `0.05` | Inspector `0–1000` |
| `use_gravity` | `True` | Gravity applies |
| `is_kinematic` | `False` | Transform-driven: pushes dynamics, never pushed |
| `freeze_pos_x/y/z` | `False` | Serialized as `freeze_pos` triple |
| `freeze_rot_x/y/z` | `False` | Serialized as `freeze_rot` triple |
| `velocity` / `angular_velocity` | `Vec3.zero()` | Readable every frame; setter marks body dirty for next sync |

## Usage in Editor

Add `Rigidbody` + one collider (`Box`/`Sphere`/`Capsule`/`Mesh`). Mass `70–90` player-scale, `1–10` crates; keep interacting mass ratios within ~100:1. Tick `is_kinematic` for movers, freeze rotation axes for non-tipping crates.

## Usage in Code

```python
entity = scene.create_entity("Ball")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.sphere_collider import SphereCollider

rb = Rigidbody()
rb.mass = 2.0
rb.drag = 0.1
rb.freeze_rot_x = True
rb.freeze_rot_z = True
entity.add_component(rb)
col = SphereCollider()
col.radius = 0.5
entity.add_component(col)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody)
    if rb is None or rb.is_kinematic:
        return
    rb.add_force(Vec3(0.0, 20.0, 0.0))   # accumulated, cleared after push
    rb.add_torque(Vec3(0.0, 1.0, 0.0))
    rb.add_impulse(Vec3(0.0, 5.0, 0.0))  # instant velocity change impulse/mass

def on_collision_enter(self, other_id, force=0.0):
    pass
def on_collision_stay(self, other_id, force=0.0):
    pass
def on_collision_exit(self, other_id, force=0.0):
    pass
```

- `add_force(force, world_space=True)` / `add_torque(torque)` accumulate until the next ECS→physics push, then auto-clear — call every fixed step for continuous thrust.
- `add_impulse` is ignored for kinematic bodies and non-positive mass.
- `velocity` setter flips `_velocity_dirty` so the value pushes into the solver on the next sync.
- Collision callbacks: script `on_collision_enter/stay/exit` accept `(other_id)` or `(other_id, force)` (arity auto-detected via `inspect.signature`); engine-component handlers always take `(other_id, force)`.

## Fixed update notes

- `dt` is `Engine._fixed_dt` (`0.02` default); render hitches never change physics `dt` (backlog capped at `5 * _fixed_dt`, max `5` steps/tick).
- Forces apply in `_sync_ecs_to_physics`, results (`pos`/`rot`/`vel`/`ang_vel`) come back in `_sync_physics_to_ecs`; kinematic and Transform-dirty bodies are skipped on read-back (Transform-driven, never overwritten).
- In `multi_threaded` mode this crosses the `SharedPhysicsBuffer` (flags `1` active, `2` dirty, `4` kinematic/teleport, `8` 2D).

## Related

Box/Sphere/Capsule/Mesh colliders, CharacterController, Joints, Solver & Threading, Collision Layers.

## Verified against

- `core/components/physics/rigidbody.py`
- `core/physics/scene/bodies.py` (`_create_entity_bodies`, `_sync_ecs_to_physics`, `_sync_physics_to_ecs`)
- `core/physics/scene/events.py` (collision callback arity)
- `core/engine/eng/loop.py`, `core/engine/eng/engine.py`
