# Projector

Projects a texture from a frustum: slide projector, fake window light, graffiti beam.

| Inspector group / field | Default | Meaning |
|---|---|---|
| Texture: texture_path | — | Projected image (png/jpg/jpeg) |
| Texture: color | white | Tint multiplier |
| Texture: intensity | 1.0 | Brightness, 0–1000 |
| Projection: range | 10.0 | Throw distance |
| Projection: spot_angle | 30° | Frustum angle, 1–179 |
| Projection: aspect_ratio | 1.0 | Frustum width/height |
| Projection: near_plane / far_plane | 0.1 / 100 | Depth slice |
| Options: flip_x / flip_y | off / on | Image mirroring |
| Options: cast_shadows | on | Shadowed projection |

The viewport gizmo draws the projection cone with aspect-correct base. Aim with the Transform like a Spot Light.

Related: Spot Light, Shadows (projector shadows pass).
