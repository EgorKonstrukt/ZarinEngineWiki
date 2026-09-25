# Particle System

CPU-simulated, GPU-drawn particles: sparks, smoke, magic, debris. A GPU-driven playback path (`ParticleGpu` shader) exists for heavy systems.

| Inspector group | Covers |
|---|---|
| Emission | Duration, Looping, Prewarm, Start Delay |
| Start | Start Lifetime (Min/Max), Start Speed (Min/Max), Start Size (Min/Max) and further initial state |

Tune emission, appearance (size and color over life, texture) and physics (gravity factor, drag) in the Inspector. The system renders its gizmo representation directly in the viewport, so shape and direction are visible without pressing Play. Trigger bursts or loops from scripts at runtime.

Performance: prefer a few large GPU-driven systems over many small CPU ones; share one texture where possible; watch overdraw from big soft transparent quads.

Related: Particle Force Field, Object Effects, Materials (particle textures).
