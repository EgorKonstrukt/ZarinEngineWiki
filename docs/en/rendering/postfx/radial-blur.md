# Radial Blur

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Zoom-burst smear outward from a screen center point.

## Parameters

Center, strength, sample feel.

## Tips

Warp charges, impact frames, focus pulls. Animate strength 0→1→0.

## Cost

Medium by samples.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
