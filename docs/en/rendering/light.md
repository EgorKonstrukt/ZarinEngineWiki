# Light (Base)

Base component for all lights. The concrete type is chosen by the subclass component on the same entity.

| Field | Default | Meaning |
|---|---|---|
| color | white | Light tint |
| intensity | 100000.0 | Physical units, see subtype pages |
| cast_shadows | on | Write into shadow maps |
| range | 10.0 | Falloff distance for point/spot/area |
| spot_angle / spot_inner_angle | 30 / 20 | Spot cone, degrees |
| area_type | RECT | RECT or DISK for area lights |
| area_width / area_height | 1×1 | Emitting surface size |
| area_double_sided | off | Emit from both faces |
| area_samples | 6 | Soft shadow samples |
| area_shadow_bias | 0.005 | Shadow acne offset |

Subtypes: Directional Light (lux), Point Light (lumens), Spot Light (lumens), Area Light (nits), plus the Projector component for textured projection. All lights render gizmo helpers in the viewport.

Related: Shadows, Skybox and Procedural Sky, PostFX Fog and God Rays.
