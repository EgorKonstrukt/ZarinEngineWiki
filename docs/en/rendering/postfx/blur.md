# Blur

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Separable gaussian soften of the whole frame, radius-controlled.

## Parameters

Radius, passes.

## Tips

Backgrounds behind modal dialogs, blur-in transitions, softening harsh procedural detail.

## Cost

Medium-high by radius. Prefer misted materials over fullscreen blur for static softness.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
