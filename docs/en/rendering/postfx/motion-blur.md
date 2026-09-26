# Motion Blur

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Smears fast-moving pixels along screen velocity for perceived smoothness.

## Parameters

Shutter feel/strength, max samples.

## Tips

Racing, fast camera swings. Keep subtle or UI smears with it.

## Cost

Medium-high by sample count.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
