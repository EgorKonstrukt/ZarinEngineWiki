# FSR2

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

FidelityFX Super Resolution 2 temporal upscaling: render small, reconstruct sharp.

## Parameters

Quality preset, sharpness.

## Tips

Performance mode on weak GPUs; render_scale 0.5–0.67 with FSR2 beats native blur.

## Cost

Medium, but buys back more than it costs.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
