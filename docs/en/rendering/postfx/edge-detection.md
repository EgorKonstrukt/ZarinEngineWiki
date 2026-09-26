# Edge Detection

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Highlights luminance discontinuities for inked-outline stylization.

## Parameters

Threshold, line color/width feel.

## Tips

Comic mode with Hatching and Quantize; blueprint mode with Sobel.

## Cost

Cheap.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
