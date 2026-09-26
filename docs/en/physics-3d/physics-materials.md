# Physics Materials

Surface response per collider: friction grips, bounciness rebounds. Two ways to set them — asset or inline — with one rule: the asset wins when assigned.

- Asset filter `Physics Materials (*.zphysmat)`, referenced through `physic_material` on any collider. Share one ice asset across the whole rink instead of tuning ten colliders.
- Without an asset, inline `material_friction` (0.6) and `material_bounciness` (0.0) apply.
- Body creation passes friction and restitution to the solver on spawn; later changes re-register the shape, so mid-game swaps cost a hitch — preassign variants instead.

Preset cookbook:

- Ice rink: friction 0.03–0.08, bounce 0 — glide with steering from scripts.
- Rubber ball court: friction 0.7–0.9, bounce 0.7–0.85 — lively but controllable.
- Metal hangar: friction 0.25–0.35, bounce 0.05–0.15 — crates slide when pushed, stop on their own.
- Mud: friction 1.0+, bounce 0 — use high drag on the Rigidbody too.
- Trampoline: bounce 0.9–0.95 on a trigger-adjacent pad plus an upward impulse script (pure bounce never exceeds incoming energy).

Combine pairs thoughtfully: the contact solver blends both materials, so an icy ball (0.05) on ice (0.05) glides while the same ball on rubber (0.9) grips.

Troubleshooting: slides everywhere → global friction too low, check the floor material first; dead bounce → restitution lost in the pair, raise both sides; changes ignored at runtime → shape re-registration pending, re-enter Play or reassign.

Related: Collision Layers, Box/Sphere/Capsule/Mesh Collider pages, Rigidbody (drag interplay).
