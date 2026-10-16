# Buoyancy

Floating bodies for ponds, rivers and oceans: hulls displace water, waves rock them, currents push them. CPU sampling in `on_fixed_update` — not a solver body.

## Properties

| Field | Default | Notes |
|---|---|---|
| `density` | `500.0` | Inspector `1–20000`; object density |
| `volume` | `0.0` | Override; `0` = auto from collider (box `sx*sy*sz`, sphere `4/3πr³`, capsule `4/3πr³ + πr²h`) |
| `water_density` | `1000.0` | Inspector `1–2000` |
| `linear_drag` | `8.0` | Inspector `0–50` (deserialize fallback `1.5`) |
| `angular_drag` | `6.0` | Inspector `0–50` (deserialize fallback `1.2`) |
| `flow_influence` | `1.0` | Inspector `0–10` |
| `use_waves` / `use_flow` | `True` / `True` | Sample `height_at` / `flow_at` from `WaterVolume` |
| `sample_resolution` | `8` | Inspector `2–24` grid per axis |
| `max_acceleration` | `8.0` | × g clamp on net acceleration |
| `enable_debug` | `False` | Fills `gizmo_lines` with sample points |
| Read-only | `submerged_fraction` | `0–1` displaced/total volume |

## Usage in Editor

Add `Rigidbody` (non-kinematic) + `Box`/`Sphere`/`CapsuleCollider` + `Buoyancy`, place a `WaterVolume` in the scene. Tune `density` vs `water_density`, `linear_drag` for hull feel.

## Usage in Code

```python
entity = scene.create_entity("Boat")
from core.components.physics.rigidbody import Rigidbody
from core.components.physics.box_collider import BoxCollider
from core.components.physics.buoyancy import Buoyancy

rb = Rigidbody()
rb.mass = 500.0
entity.add_component(rb)
box = BoxCollider()
entity.add_component(box)
b = Buoyancy()
b.density = 500.0
b.linear_drag = 8.0
b.sample_resolution = 8
entity.add_component(b)

def on_fixed_update(self, dt):
    # Buoyancy.on_fixed_update runs itself: Archimedes + quadratic/viscous
    # drag + flow-relative velocity, clamped to max_acceleration * 9.81.
    frac = b.submerged_fraction
```

Requires a collider shape (build fails without one) and at least one `WaterVolume` entity, else `submerged_fraction` stays `0`.

## Fixed update notes

- Runs in component `on_fixed_update(dt)` with `dt = Engine._fixed_dt` (`0.02` default); force path is `rb.add_force` / `rb.add_torque`, applied on the next physics push. Skipped when kinematic or Transform-less.
- Gravity magnitude used internally: `_GRAVITY_MAG = 9.81`.

## Related

Rigidbody, Box/Sphere/Capsule colliders, WaterVolume (environment), Solver & Threading.

## Verified against

- `core/components/physics/buoyancy.py`
