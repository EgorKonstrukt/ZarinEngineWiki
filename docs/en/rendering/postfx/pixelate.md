# Pixelate

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Retro mosaic downsampling with adjustable cell size.

## Parameters

Cell size.

## Tips

Retro modes with Scanlines and Dithering; pair with low render_scale for honest pixels.

## Cost

Cheap (fewer pixels, in fact).

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
