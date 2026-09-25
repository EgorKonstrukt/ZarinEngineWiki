# Area Light

Soft rectangular or disk emitter for studios, windows and panels.

- Intensity in nits, default 100. Reference: SDR monitor 200, office panel 500, overcast sky 2000, clear sky 5000, LED softbox 10000.
- `area_type` RECT or DISK, size from `area_width` / `area_height`, `area_double_sided` for two-face emission.
- Softness from `area_samples` (default 6); `area_shadow_bias` 0.005 against acne.

Area lights are the most expensive light type — use them as hero sources and fill the rest with points and spots.

Related: Light (Base), Shadows, Dynamic Cubemap notes in Skybox.
