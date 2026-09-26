# Color Grading

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Final tone and balance transform: lift/gamma/gain style mapping plus saturation and contrast.

## Parameters

Tone wheels or lift/gamma/gain, saturation, contrast, tint.

## Tips

One grading per scene mood; animate between presets on biome change instead of stacking two.

## Cost

Cheap single pass. Always on.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
