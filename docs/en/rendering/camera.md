# Camera

Renders the scene from an entity viewpoint. Add one per view: main gameplay camera, UI-plane camera, cutscene cameras.

| Inspector group / field | Meaning |
|---|---|
| Projection: FOV | Perspective field of view, 1–179°, default 60 |
| Projection: Near / Far | Clip planes, defaults 0.01 / 1000 |
| Projection: Ortho Size | Orthographic vertical half-extent, default 5 |
| Rendering: Depth | Draw-order priority, −100…100 |
| Render Resolution: Resolution Mode | Native or Custom |
| Render Resolution: Viewport Width / Height | Custom size, e.g. 1920×1080 |
| render_scale | Resolution multiplier 0.1–1.0 |

Math: view is `look_at(position, position + forward, up)`; projection is `perspective(fov, aspect, near, far)` or `orthographic(-half_w, half_w, -ortho_size, ortho_size, near, far)` with `half_w = ortho_size * aspect`.

Related: Editor Camera (scene editing only), Lights and Shadows, Render Resolution modes in build profiles.
