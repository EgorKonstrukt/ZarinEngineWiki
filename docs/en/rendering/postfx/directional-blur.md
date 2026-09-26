# Directional Blur

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Smears pixels along one screen vector — speed lines without particles.

## Parameters

Angle, length, center bias.

## Tips

Dash trails, warp jumps, slash arcs. Fades to zero length when idle.

## Cost

Medium by length. Short bursts only.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
