# Spot Light

Cone light for flashlights, stage lamps, headlights and window shafts. Direction from the Transform rotation, cone from `spot_angle` (outer) and `spot_inner_angle` (full-bright core).

- Intensity in lumens, default 1000 — same scale as Point Light, so fixtures match when swapping types.
- Up to 4 simultaneous spot shadow maps (1024 default) — one face each, far cheaper than point cubes. Spots are the economical shadowed light: prefer them whenever directionality is acceptable.
- Gizmo shows the cone with the inner-angle ring; aim by rotating the entity, narrow `spot_angle` for theater beams, widen for flood coverage.

Workflows: flashlight — spot parented to the camera with shadows on and angle ~25°; stage — several narrow spots from a truss with animated colors; car headlights — two spots with range matched to braking distance; window shafts — wide-angle spot angled through a frame plus a Projector for the frame pattern.

Animate `spot_angle` for zoom effects and `intensity` for flicker; both are cheap uniform updates with no re-registration.

Related: Light (Base), Shadows, Projector (textured cone), Point Light.
