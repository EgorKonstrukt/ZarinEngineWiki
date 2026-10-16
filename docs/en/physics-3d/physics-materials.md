# Physics Materials

Surface response per collider: friction grips, bounciness rebounds. Asset or inline — the asset wins when assigned.

## Properties

| Field | Default | Notes |
|---|---|---|
| `dynamic_friction` | `0.6` | `≥0` |
| `static_friction` | `0.6` | `≥0` |
| `bounciness` | `0.0` | `≥0` |
| `friction_combine` | `average` | `average` / `minimum` / `maximum` / `multiply` |
| `bounce_combine` | `average` | Same set |
| File | `.zphysmat` (JSON) | `PhysicsMaterial.save/load/load_cached` |

## Usage in Editor

Create a `.zphysmat` asset, reference it via `Physic Material` on any collider (`Box`/`Sphere`/`Capsule`/`Mesh`/`Terrain`/`GSVolume`/2D). Share one ice asset across the rink instead of tuning ten colliders.

## Usage in Code

```python
entity = scene.create_entity("Ice")
from core.components.physics.box_collider import BoxCollider
from core.assets.physics_material import PhysicsMaterial

mat = PhysicsMaterial("Ice")
mat.dynamic_friction = 0.05
mat.static_friction = 0.05
mat.bounciness = 0.0
mat.save("assets/ice.zphysmat")

box = BoxCollider()
box.physic_material = "assets/ice.zphysmat"  # asset wins over inline
box.material_friction = 0.6                  # fallback when asset missing
box.material_bounciness = 0.0
entity.add_component(box)
```

Resolution rule (verified): `resolve_physics_material` loads `physic_material` via `load_cached`; on missing/empty path it falls back to `material_friction`/`material_bounciness` with combine `average`. Combine math: `minimum`/`maximum`/`multiply` as named, default `average = (a+b)/2`.

## Fixed update notes

- Materials resolve at body-creation time (`_shape_info_for`); changing the asset later rebuilds via the 60-step shape check. No per-step cost.

## Related

All collider pages, Collision Layers (response vs. filtering), Solver & Threading.

## Verified against

- `core/assets/physics_material.py`
- `core/physics/shapep/materials.py` (`resolve_physics_material`, `default_physics_material_dict`)
