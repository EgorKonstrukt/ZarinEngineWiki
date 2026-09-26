# Shadows

Shadow-map subsystem behind every `cast_shadows` toggle — depth rendering from the light point of view, sampled back in the main pass.

- Directional: cascaded map, default 4 cascades with resolutions 2048/1024/1024/512, distance 50. Cascades concentrate texels near the camera; distant ground gets coarser depth.
- Point: cube maps, 512 default, up to 4 simultaneous shadowed point lights.
- Spot: perspective maps, 1024 default, up to 4 simultaneous.
- Projector shadows render in their own pass (`render_projector_shadows`).
- Each cascade/face owns a depth texture plus framebuffer; the pass supports GPU instancing with a flat culling path, and a debug overlay exists (`ShadowDebug` shader).
- Bias controls: global settings plus per-light bias such as `area_shadow_bias` (0.005 default on area lights).

Tuning order, step by step: set shadow distance to cover the gameplay area and nothing more; pick the smallest acceptable resolution per type; nudge bias until acne leaves without peter-panning (detached shadows); finally cap simultaneous shadowed lights — extra ones should be non-shadowed fill.

Cost model: the shadow pass redraws the scene per cascade and per shadowed face, so 4 cascades plus 2 shadowed spots means 6 extra scene draws. Profile it as its own stage before blaming the main pass.

Troubleshooting: acne (striped self-shadow) → raise bias slightly; peter-panning (floating contact) → bias too high, lower it; flickering cascades → distance too large for the resolution, shrink distance or raise resolution; no shadows at all → light outside the 8-light pack, beyond distance, or toggle off.

Related: Light (Base), Directional/Point/Spot/Area pages, Projector, Cameras (near/far affect depth precision too).
