# Shadows

Shadow-map subsystem behind every `cast_shadows` toggle.

- Directional: cascaded map, default 4 cascades with resolutions 2048/1024/1024/512, distance 50.
- Point: cube maps, 512 default, up to 4 simultaneous.
- Spot: 1024 default, up to 4 simultaneous.
- Each cascade/face owns a depth texture plus framebuffer; the pass supports GPU instancing with a flat culling path.
- Bias controls: global settings plus per-light `area_shadow_bias` (0.005 default on area lights).

Tuning order: set distance to cover the gameplay area, pick the smallest acceptable resolution, then nudge bias until acne leaves without peter-panning. Profile the shadow pass separately — it redraws the scene per cascade/face.

Related: Light (Base), Directional/Point/Spot/Area pages, Projector.
