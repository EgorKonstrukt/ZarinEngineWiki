# Directional Light

Sun-style parallel light. Position only sets the gizmo origin; direction comes from the Transform rotation.

- Intensity in lux, default 100000 (direct sun). Reference: full daylight 15000, overcast 5000, sunrise 400, office 500, twilight 10, full moon 0.2.
- `procedural_sky_lighting` couples the sun to the procedural sky: rotating the light moves the sun and rebalances ambient.
- Shadows use the cascaded map (default 4 cascades, resolutions 2048/1024/1024/512, distance 50).

Typical setup: one directional sun with shadows on as the key light, plus ambient or sky fill.

Related: Light (Base), Shadows, Procedural Sky, Atmosphere.
