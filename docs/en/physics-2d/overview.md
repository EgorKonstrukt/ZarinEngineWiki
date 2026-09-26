# Physics 2D Overview

A separate lightweight 2D pipeline with its own components, simulated by the same solver but flagged as 2D: out-of-plane velocity (`vz`, tilt rates) is zeroed every step so bodies never drift off-plane.

Use it for side-scrollers, top-down games and 2D UI-plane physics where full 3D bodies would be overkill and harder to constrain. 2D shapes map onto the solver as flat primitives: `Box2D → box` with size `[sx, sy, 1.0]` and center `[ox, oy, 0]`, `Circle2D → sphere`.

Scripts use the same collision callbacks (`on_collision_enter/stay/exit`), the same fixed step (`dt = 0.02`), and the same layer/mask filtering as 3D. Entity rule: one pipeline per body — never mix 2D and 3D components on the same entity.

Platformer starter: static ground (BoxCollider2D, no Rigidbody2D), player (Rigidbody2D + BoxCollider2D, freeze rotation on), coins as trigger circles with `is_trigger` reporting pickups to scripts.

Pages: Rigidbody 2D, Box Collider 2D, Circle Collider 2D.

Related: Physics 3D Overview (shared solver and threading), Collision Layers, Script Examples (Driver adapts to 2D by dropping the Z axis).
