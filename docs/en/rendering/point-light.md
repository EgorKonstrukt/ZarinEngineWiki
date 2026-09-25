# Point Light

Omnidirectional bulb. Position comes from the Transform; light falls off with `range`.

- Intensity in lumens, default 1000. Reference: candle ~12.6, decorative LED 100, desk lamp 300, room ceiling 800, 100 W bulb 1600, street light 15000.
- Up to 4 simultaneous point shadow maps (512 default); enable `cast_shadows` only where the shadows are visible.
- Gizmo shows the range sphere.

Use for lamps, explosions, pickups and any local glow. For large interiors prefer a few bright points over many dim ones.

Related: Light (Base), Shadows, Spot Light.
