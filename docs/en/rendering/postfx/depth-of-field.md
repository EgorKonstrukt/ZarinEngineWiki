# Depth of Field

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Separates a focus plane from fore/background blur using the depth buffer and a focus distance.

## Parameters

Focus distance, aperture/f-stop feel, max blur radius.

## Tips

Cutscenes, dialogue close-ups, weapon inspection. Drive focus distance from a script to the aimed target.

## Cost

High: gather taps over depth. Narrow the max radius before blaming the GPU.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
