# God Rays

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Radial light shafts fanning from bright sources, occluded by depth.

## Parameters

Density, decay, exposure feel, source threshold.

## Tips

Forest sun shafts, cathedral windows, dusty halls. Needs a bright source in frame.

## Cost

Medium-high: radial samples. Shorten shafts before cutting the effect.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
