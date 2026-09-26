# Camera

Renders the scene from an entity viewpoint. Add one per view: main gameplay camera, UI-plane camera, cutscene cameras, security-monitor walls.

| Inspector group / field | Meaning |
|---|---|
| Projection: FOV | Perspective field of view, 1–179°, default 60 |
| Projection: Near / Far | Clip planes, defaults 0.01 / 1000 |
| Projection: Ortho Size | Orthographic vertical half-extent, default 5 |
| Rendering: Depth | Draw-order priority, −100…100; higher draws later (on top) |
| Render Resolution: Resolution Mode | Native (follow viewport) or Custom |
| Render Resolution: Viewport Width / Height | Custom size, e.g. 1920×1080 |
| render_scale | Resolution multiplier 0.1–1.0 |

Math: view is `look_at(position, position + forward, up)`; projection is `perspective(fov, aspect, near, far)` or `orthographic(-half_w, half_w, -ortho_size, ortho_size, near, far)` with `half_w = ortho_size * aspect`. Gizmo draws the frustum in the viewport.

Workflows:

- Main camera: perspective, FOV 55–70, near 0.1, far matched to the level (500 for interiors, 2000+ for open fields), depth 0.
- UI-plane camera: orthographic, ortho size matched to design units, depth above the main camera so HUD draws last.
- Cutscenes: several cameras with staggered depth, cross-fade by animating depth or enabling in sequence from a script.
- Pixel look: render_scale 0.25–0.5 with FXAA off and Pixelate post effect.

Depth precision: the near plane dominates. A near of 0.001 with far 10000 guarantees shimmer; keep near as large as the scene allows (0.1 outdoors, 0.01 interiors) and far as small as possible.

Troubleshooting: nothing renders → camera disabled, depth buried under a clear-everything camera, or culling mask excluding all layers; stretched image → aspect comes from the viewport, check custom resolution; jitter at distance → float64 scene vs too-tight near/far, widen the ratio.

Related: Editor Camera, Lights and Shadows, Render Resolution modes, PostFX (Pixelate, Panini for wide FOV).
