# Particle System

CPU-simulated, GPU-drawn particles for sparks, smoke, magic, debris, weather and ambient dust. A GPU-driven playback path (`ParticleGpu` shader) exists for heavy systems that would swamp the CPU.

| Inspector group | Covers |
|---|---|
| Emission | Duration, Looping, Prewarm, Start Delay — when and how long the emitter runs |
| Start | Start Lifetime (Min/Max), Start Speed (Min/Max), Start Size (Min/Max) and further initial state per particle |
| Appearance | Size and color over life, texture |
| Physics | Gravity factor, drag, collision response |

The system renders its gizmo representation directly in the viewport — emitter shape, direction cone and range read without pressing Play. Scrub emission rate and watch the gizmo density change.

Workflows: campfire — short lifetime, upward speed, orange-to-transparent color ramp, looping with prewarm so it starts full; explosion — one-shot burst (looping off) with high speed spread plus a Point Light flash; rain — huge count, thin stretched texture, GPU path, no collision; magic trail — emitter parented to the projectile, small count, additive-style material.

Trigger bursts or loops from scripts at runtime through the component API; drive intensity from gameplay (engine RPM → exhaust rate).

Performance rules: prefer a few large GPU-driven systems over many small CPU ones; share one texture across systems; watch overdraw — big soft transparent quads are the usual fill-rate killer; kill emitters offscreen via distance culling or manual enable.

Troubleshooting: invisible → texture missing, size zero, or lifetime zero; clumping at origin → speed zero or direction unset; runs once then stops → looping off on an ambient effect; editor shows but game hides → layer mask excludes the game camera.

Related: Particle Force Field, Object Effects (spawn bursts), Materials (particle textures), Point Light (explosion flash).
