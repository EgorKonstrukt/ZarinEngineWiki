# SSAO

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Screen-space ambient occlusion darkening contact crevices the ambient term cannot see.

## Parameters

Radius, intensity, sample feel.

## Tips

Grounding interiors and clutter; keep radius small or corners glow-halo.

## Cost

High. First candidate to cut on weak GPUs.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
