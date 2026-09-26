# Film Grain

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Animated fine noise over the frame, stronger in shadows by default.

## Parameters

Size, amount, shadow bias, animation speed.

## Tips

Cinematic finish over Bloom + Color Grading; animate amount up in dark zones.

## Cost

Cheap.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
