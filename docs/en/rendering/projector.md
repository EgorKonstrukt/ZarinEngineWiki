# Projector

Projects a texture from a frustum: slide projector, fake window light, graffiti beam, logo thrower.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Texture: texture_path | — | Projected image (png/jpg/jpeg) |
| Texture: color | white | Tint multiplier |
| Texture: intensity | 1.0 | Brightness, 0–1000 |
| Projection: range | 10.0 | Throw distance |
| Projection: spot_angle | 30° | Frustum angle, 1–179 |
| Projection: aspect_ratio | 1.0 | Frustum width/height |
| Projection: near_plane / far_plane | 0.1 / 100 | Depth slice of the throw |
| Options: flip_x / flip_y | off / on | Image mirroring |
| Options: cast_shadows | on | Occluders block the projection |

The viewport gizmo draws the projection cone with an aspect-correct base rectangle — frame the target surface visually, then fine-tune angle and range. Aim with the Transform exactly like a Spot Light; the throw slice (near/far) clips the projection so it never leaks through back walls.

Workflows: cinema — projector entity behind the audience aimed at a white VideoRenderer-style screen; fake windows — bright projector with a frame texture plus a soft spot for spill; decals-in-motion — animated texture offset is not built in, swap `texture_path` from a script for slideshow projection.

Cost: one extra frustum sample set per projector; shadowed projectors join the projector shadow pass. Keep counts low (1–3) and ranges tight.

Troubleshooting: mirrored image → toggle flip_x/flip_y; projection through walls → pull far_plane in; washed out → intensity competes with the key light, raise it or dim the scene.

Related: Spot Light, Shadows (projector shadows pass), VideoRenderer (screen surfaces), TextRenderer (static slides alternative).
