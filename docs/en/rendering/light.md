# Light (Base)

Base component for all lights. The concrete type is chosen by the subclass component on the same entity: Directional, Point, Spot or Area. Shared fields live here once.

| Field | Default | Meaning |
|---|---|---|
| color | white | Light tint, multiplies every contribution |
| intensity | 100000.0 | Physical units — lux, lumens or nits depending on subtype |
| cast_shadows | on | Write into shadow maps; the main per-light cost switch |
| range | 10.0 | Falloff distance for point/spot/area (directional ignores it) |
| spot_angle / spot_inner_angle | 30 / 20 | Spot cone outer/inner, degrees |
| area_type | RECT | RECT or DISK emitting shape for area lights |
| area_width / area_height | 1×1 | Emitting surface size |
| area_double_sided | off | Emit from both faces |
| area_samples | 6 | Soft shadow sample count |
| area_shadow_bias | 0.005 | Shadow acne offset |

How it reaches the shader: the scene collector packs up to 8 lights into uniform arrays (type, position, direction, color, intensity, range, spot angles, area size) plus ambient. Over-limit lights are culled by priority — keep hero lights few and let the rest be non-shadowed fill.

All lights render gizmo helpers (direction arrows, range spheres, cones, area quads) so lighting can be staged visually.

Workflow: block the key light first (usually one shadowed directional), add fill points/spots without shadows, finish with a rim or area accent. Match intensities to the unit scale — a 100000-lux sun next to a 1.0-intensity point reads correctly; two suns at 100000 fight.

Troubleshooting: scene too dark → ambient low and no fill; blown out → two hero lights overlapping, halve one; shadows missing → cast_shadows off, beyond shadow distance, or past the 4+4 shadow budget.

Related: Directional/Point/Spot/Area pages, Projector, Shadows, Skybox and Atmosphere.
