# Collision Layers

Who collides with whom, independent of surface response. Every collider carries `layer` (default `0`) and `mask` (default `0xFFFF`); contact requires both sides to accept each other.

## Properties

| Field | Default | Notes |
|---|---|---|
| `MAX_LAYERS` | `16` | Layers `0–15` |
| `layer` | `0` | Per-collider index |
| `mask` | `0xFFFF` | Per-c Collider bitmask |
| Default names | `Default, TransparentFX, Ignore Raycast, Water, UI, Player, Enemy, Projectile, Trigger, Ground, Layer10–15` | `DEFAULT_LAYER_NAMES` |
| Default matrix | All-vs-all except exclusions | `build_default_collision_matrix()`: layer `2` (Ignore Raycast) collides with nothing; layer `4` (UI) collides only with itself |

## Usage in Editor

Set `Layer` + `Collision Mask` on each collider. Typical plan: `0` World (everything), `5` Player, `6` Enemy (mute enemy-vs-enemy for crowds), `7` Projectile (world + characters only), `8` Trigger pickups (player only).

## Usage in Code

```python
entity = scene.create_entity("Pickup")
from core.components.physics.sphere_collider import SphereCollider

s = SphereCollider()
s.radius = 1.0
s.is_trigger = True
s.layer = 8
s.mask = 1 << 5  # only player layer
entity.add_component(s)

from core.physics.collision_layers import build_default_collision_matrix, get_layer_mask
matrix = build_default_collision_matrix()
mask = get_layer_mask([], matrix, 0)

def on_collision_enter(self, other_id, force=0.0):
    pass  # triggers report through the same mask test, no contact response
```

Helpers: `layer_mask_from_names(names, indices)`, `layer_names_from_mask(names, mask)`.

## Fixed update notes

- `layer`/`mask` pass into every `create_rigid_body` / `create_compound_rigid_body` / soft-body call and are evaluated solver-side per step.
- `per_layer_process` mode can additionally isolate layers into on-demand solver processes (`plugin._get_layer_process`).

## Related

Physics Materials, Solver & Threading (per-layer mode), all collider pages, Joints.

## Verified against

- `core/physics/collision_layers.py`
- `core/physics/physics_solver.py` (`collision_layer`, `collision_mask` params)
- `plugins/physics_plug/plugin.py` (per-layer processes)
