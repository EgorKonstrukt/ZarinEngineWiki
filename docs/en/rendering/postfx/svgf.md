# SVGF

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Spatiotemporal variance-guided denoiser that cleans noisy effects (SSR, soft shadows) across frames.

## Parameters

History length, variance feel.

## Tips

Always enable alongside SSR; mandatory for stable raytraced previews.

## Cost

Medium. Pays for itself under SSR.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
