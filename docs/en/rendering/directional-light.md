# Directional Light

Sun-style parallel light. Position only sets the gizmo origin; direction comes entirely from the Transform rotation, and every surface receives the same direction regardless of distance.

- Intensity in lux, default 100000 (direct sun). Reference scale: full daylight 15000, overcast 5000, sunrise/sunset 400, office interior 500, twilight 10, full moon 0.2.
- `procedural_sky_lighting` couples the sun to the procedural sky: rotating the light moves the sun disc and rebalances ambient automatically — day cycles become one rotation animation.
- Shadows use the cascaded map (default 4 cascades at 2048/1024/1024/512, distance 50). Because one light covers the whole scene, its shadow pass is usually the most expensive single light — keep exactly one shadowed directional.

Workflows: outdoor day — sun intensity 80000–120000, warm color, shadows on, procedural sky coupled; night — dim blue directional (5–50 lux range feel) with shadows off plus a few shadowed spots for pools of light; interior — directional off or very low, spots and points carry the scene.

The viewport gizmo shows direction arrows and the shadow camera box; if the box does not cover the gameplay area, shadows clip — shrink shadow distance or move the action inside.

Related: Light (Base), Shadows, Procedural Sky, Atmosphere, Clouds (shadow strength interplay).
