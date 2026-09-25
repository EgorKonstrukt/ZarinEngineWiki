# Rigidbody

Makes an entity simulated: mass, gravity response, accumulated forces, readable velocities.

Inspector fields:

| Field | Default | Meaning |
|---|---|---|
| `mass` | 1.0 | Body mass; forced to 0 when kinematic |
| `drag` | 0.0 | Linear damping |
| `angular_drag` | 0.05 | Angular damping |
| `use_gravity` | True | Gravity applies |
| `is_kinematic` | False | Driven by Transform, pushes others but is not pushed |

Plus per-axis freeze toggles for position and rotation, serialized as `freeze_pos` / `freeze_rot` triples.

Script API:

```python
rb = self._entity.get_component_by_name("Rigidbody")
if rb:
    rb.add_force(Vec3(0.0, 10.0, 0.0))
    rb.add_torque(Vec3(0.0, 1.0, 0.0))
    rb.add_impulse(Vec3(0.0, 5.0, 0.0))
    v = rb.velocity
```

- `velocity` / `angular_velocity` — readable every frame, writable (marks the body dirty so the new value is pushed to the solver).
- `add_force` / `add_torque` — accumulate until the next step, then cleared.
- `add_impulse` — instant velocity change `impulse / mass`; ignored for kinematic bodies and non-positive mass.

Setup rule: a dynamic body needs `Rigidbody` (positive mass, non-kinematic) plus a collider. A collider alone is static scenery. Body creation reads `mass` and `is_kinematic`; velocities and forces sync every step.
