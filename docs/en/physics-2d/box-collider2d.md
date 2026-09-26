# Box Collider 2D

Rectangle shape in plane: platforms, walls, crates, hitboxes and trigger zones.

| Inspector group / field | Meaning |
|---|---|
| Collision: Layer / Collision Mask | Filtering |
| Shape: Offset | In-plane offset `[ox, oy]` from the pivot |
| Shape: Size | In-plane extents |
| Is Trigger | Overlap events without contact response |
| Material: Physic Material | `.zphysmat` asset override |

Maps onto the solver as a flat box `[sx, sy, 1.0]` with center `[ox, oy, 0]`. Combine with Rigidbody2D; never mix 2D and 3D components on one entity.

Patterns: ground — wide thin box, static, friction high; one-way platform — box on a player-excluded layer plus a top trigger enabling collision from above via script; crates — small boxes with dynamic Rigidbody2D mass 1–3; checkpoints — tall thin triggers spanning the track.

Pixel-perfect tip: size colliders in whole design units and keep pixel-to-unit scale consistent, or visual/collision edges drift apart at large coordinates.

Troubleshooting: falls through fast movers → tunneling, cap speed or lower fixed dt; trigger silent → mask mismatch; edges catch the player → adjacent boxes overlap slightly, butt-join them exactly or merge into one.

Related: Rigidbody 2D, Circle Collider 2D, Box Collider (3D), Collision Layers.
