# Area Light

Soft rectangular or disk emitter for studios, windows, softboxes and panels — the closest to real-world fixtures.

- Intensity in nits, default 100. Reference: SDR monitor 200, office panel 500, overcast sky 2000, clear sky 5000, LED softbox 10000.
- `area_type` RECT or DISK; size from `area_width` / `area_height`; `area_double_sided` emits from both faces (thin panels, windows).
- Softness from `area_samples` (default 6) — more samples, softer and costlier shadows; `area_shadow_bias` 0.005 against acne.

Area lights are the most expensive light type per unit: use them as hero sources (one soft key through a window, one panel over a desk) and fill the rest with points and spots. Gizmo draws the emitting quad so placement against windows reads directly.

Workflow for a soft interior: area light outside the window aimed in (double-sided off), cool ambient or sky fill, one warm unshadowed point inside for bounce feel. Tune width/height to the actual window — mismatched sizes break the soft-shadow direction cue.

Related: Light (Base), Shadows, Skybox (IBL fill alternative), Dynamic Cubemap notes.
