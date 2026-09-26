# FXAA

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Fast approximate anti-aliasing: edge detection plus targeted smoothing in one pass.

## Parameters

Quality/subpixel settings.

## Tips

Default AA for clean 3D; disable for pixel-art looks.

## Cost

Cheap.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
