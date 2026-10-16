# 2D Guide

Top-down / side-view planar physics. 2D bodies use `Rigidbody2D` + one 2D collider and are simulated by the shared solver, then locked to the plane every step.

## Properties

| Field | Default | Notes |
|---|---|---|
| `Rigidbody2D.mass` | `1.0` | `add_impulse` divides by mass unless kinematic |
| `drag` | `0.0` | Inspector `0–1000` |
| `angular_drag` | `0.05` | Inspector `0–1000` |
| `gravity_scale` | `1.0` | Inspector `0–100` |
| `is_kinematic` | `False` | |
| `freeze_rotation` | `False` | Turn on for top-down characters |
| `velocity` / `angular_velocity` | `Vec2.zero()` / `0.0` | Setters mark `_velocity_dirty`; `consume_velocity_dirty()` clears it |

Shape mapping (`shapep/shapes.py:83-91`): `BoxCollider2D → box` size `[sx, sy, 1.0]`, center `[ox, oy, 0.0]`; `CircleCollider2D → sphere` radius `scaled_radius`, center `[ox, oy, 0.0]`. Never mix 2D and 3D components on one entity.

Plane lock (`scene.py:212-227`, `_constrain_2d_bodies`): after `step_simulation(dt)` every 2D body is clamped — `pos.z → 0`, rotation `(0, 0, rz)`, velocity `(vx, vy, 0)`, angular velocity `(0, 0, az)`.

## Usage in Editor

Static ground: `BoxCollider2D` only. Player: `Rigidbody2D` + `BoxCollider2D` with `Freeze Rotation` on. Pickups: trigger `CircleCollider2D` reporting via `on_collision_enter`.

## Usage in Code

```python
scene = Engine.instance().scene
player = scene.create_entity("Player2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D
from core.maths.math3d import Vec2

rb = Rigidbody2D()
rb.mass = 1.0
rb.gravity_scale = 0.0      # top-down: no gravity pull
rb.freeze_rotation = True   # no spin from hits
player.add_component(rb)
player.add_component(BoxCollider2D())

ground = scene.create_entity("Ground2D")
ground.add_component(BoxCollider2D())  # static: collider only

# scripted thrust (e.g. in on_fixed_update):
# rb.add_force(Vec2(10.0, 0.0))
# rb.add_impulse(Vec2(0.0, 5.0))
```

## Fixed update notes

- Same `dt = Engine._fixed_dt` (`0.02` default) as 3D; `_constrain_2d_bodies` runs after the solver step.
- Same `on_collision_enter/stay/exit` callbacks and `layer`/`mask` filtering as 3D.

## Related

Rigidbody2D, Box/Circle Collider 2D, Collision Callbacks, Collision Layers.

## Verified against

- `core/components/physics2d/rigidbody2d.py:12-104` (fields, defaults, force/impulse API)
- `core/physics/shapep/shapes.py:83-97` (`BoxCollider2D→box [sx,sy,1.0]`, `CircleCollider2D→sphere`)
- `core/physics/scene/scene.py:212-227` (`_constrain_2d_bodies` clamp)
