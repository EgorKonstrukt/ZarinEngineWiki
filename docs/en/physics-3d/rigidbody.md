# Rigidbody

Makes an entity simulated: mass, gravity response, accumulated forces, readable velocities. Without it a collider is static scenery; with it the entity joins the dynamics world.

Inspector fields:

| Field | Default | Inspector range | Meaning |
|---|---|---|---|
| mass | 1.0 | 0.001–100000 | Body mass; forced to 0 when kinematic |
| drag | 0.0 | 0–1000 | Linear damping (air, water feel) |
| angular_drag | 0.05 | 0–1000 | Spin damping |
| use_gravity | on | — | Gravity applies |
| is_kinematic | off | — | Transform-driven: pushes dynamics, never pushed |

Plus per-axis freeze toggles for position and rotation, serialized as `freeze_pos` / `freeze_rot` triples in `.zpes`.

Script API, verified:

```python
rb = self._entity.get_component_by_name("Rigidbody")
if rb:
    rb.add_force(Vec3(0.0, 10.0, 0.0))
    rb.add_torque(Vec3(0.0, 1.0, 0.0))
    rb.add_impulse(Vec3(0.0, 5.0, 0.0))
    v = rb.velocity
```

- `velocity` / `angular_velocity` — readable every frame, writable (marks the body dirty so the value pushes into the solver on the next sync).
- `add_force(force, world_space=True)` / `add_torque(torque)` — accumulate until the next step, then cleared automatically.
- `add_impulse(impulse)` — instant velocity change `impulse / mass`; ignored for kinematic bodies and non-positive mass.
- `consume_velocity_dirty()` / `_clear_forces()` — internal sync protocol; gameplay code rarely calls these directly.

Mechanics: body creation reads `mass` and `is_kinematic` (kinematic forces mass 0); velocities and forces sync every step. Freeze flags constrain axes solver-side — frozen Y position plus gravity gives a sliding puck; frozen rotations give non-tipping crates.

Mass guide: player 70–90, crates 1–10, furniture 20–50, vehicles 500–2000. Ratios beyond ~100:1 between interacting bodies strain any solver — keep gameplay contacts within two orders of magnitude.

Troubleshooting: falls through floor → missing collider, both triggers, or tunneling at high speed (lower fixed dt or cap velocity); ignores forces → kinematic on or mass 0; drifts forever → drag 0 plus no friction, raise both; jitters on stacks → solver iterations low or mass ratio extreme.

Related: all Collider pages, CharacterController (kinematic alternative), Joints, Solver and Threading, Script Examples (Kicker).
