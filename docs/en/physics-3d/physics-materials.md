# Physics Materials

Surface response: friction and bounce, per collider, via asset or inline values.

- Asset filter `Physics Materials (*.zphysmat)`, referenced through `physic_material` on any collider.
- Without an asset, inline `material_friction` (0.6) and `material_bounciness` (0.0) apply.
- Body creation passes friction and restitution to the solver on spawn; later changes re-register the shape.

Presets to try: ice (friction ~0.05, bounce 0), rubber (friction ~0.9, bounce ~0.8), metal (friction ~0.3, bounce ~0.1).

Related: Collision Layers, Box/Sphere/Capsule/Mesh Collider pages.
