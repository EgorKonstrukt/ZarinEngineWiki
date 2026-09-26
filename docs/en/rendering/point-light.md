# Point Light

Omnidirectional bulb radiating in all directions. Position comes from the Transform; brightness falls off with `range` and the distance curve.

- Intensity in lumens, default 1000. Reference: candle ~12.6, decorative LED 100, desk lamp 300, room ceiling 800, 100 W bulb 1600, street light 15000.
- Up to 4 simultaneous point shadow cube maps (512 default) — the priciest shadow type per light because of six faces. Enable `cast_shadows` only where the shadows read (hero lamps, gameplay-critical lights).
- Gizmo shows the range sphere; tune `range` until the sphere hugs the area the lamp should affect — oversized ranges waste light budget and wash neighbors.

Workflows: room lighting — one shadowed point per room at ceiling height plus unshadowed accents; explosions — brief high-intensity point animated down to zero by a script; pickups — small-range pulsing point (animate intensity, not range, for cheap shimmer).

With physical units, a 1000-lumen point beside a 100000-lux sun behaves: sun dominates outdoors, the bulb owns interiors. Mixing two shadowed points in one small room doubles cube-map cost — prefer one shadowed plus rest plain.

Related: Light (Base), Shadows, Spot Light, Emissive Pulse (visual twin for the fixture).
