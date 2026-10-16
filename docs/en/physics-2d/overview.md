# Physics 2D Overview

Planar dynamics with its own components (`Rigidbody2D`, `BoxCollider2D`, `CircleCollider2D`), simulated by the same solver but flagged 2D: out-of-plane velocity and tilt are zeroed every step so bodies never drift off-plane.

## Properties

| Item | Value |
|---|---|
| Bodies | `Rigidbody2D` + one 2D collider; never mix 2D and 3D components on one entity |
| Shape mapping | `BoxCollider2D → box` size `[sx, sy, 1.0]` center `[ox, oy, 0]`; `CircleCollider2D → sphere` |
| Plane lock | `z = 0`, rotation `(0, 0, rz)`, velocity `(vx, vy, 0)`, spin `(0, 0, az)` |
| Callbacks / layers | Same `on_collision_enter/stay/exit` and `layer`/`mask` filtering as 3D |

## Usage in Editor

Static ground: `BoxCollider2D` only. Player: `Rigidbody2D` + `BoxCollider2D` with `Freeze Rotation` on. Coins: trigger `CircleCollider2D` reporting pickups.

## Usage in Code

```python
entity = scene.create_entity("Player2D")
from core.components.physics2d.rigidbody2d import Rigidbody2D
from core.components.physics2d.box_collider2d import BoxCollider2D

rb = Rigidbody2D()
rb.mass = 1.0
rb.freeze_rotation = True
entity.add_component(rb)
box = BoxCollider2D()
entity.add_component(box)

def on_fixed_update(self, dt):
    rb = self._entity.get_component(Rigidbody2D)
    if rb is not None:
        rb.add_force(Vec2(10.0, 0.0))

def on_collision_enter(self, other_id, force=0.0):
    pass
```

## Fixed update notes

- Same `dt = Engine._fixed_dt` (`0.02` default) and same threading path as 3D; `_constrain_2d_bodies` clamps transform/velocity after `step_simulation(dt)`; shared flag `8` marks 2D bodies.

## Related

Rigidbody2D, Box/Circle Collider 2D, Physics 3D Overview, Collision Layers, Solver & Threading.

## Verified against

- `core/components/physics2d/`
- `core/physics/scene/scene.py` (`_constrain_2d_bodies`)
- `core/physics/shapep/shapes.py` (2D shape mapping)
