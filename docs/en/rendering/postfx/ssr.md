# SSR

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Screen-space reflections on smooth surfaces, resolved from the rendered frame itself.

## Parameters

Roughness cutoff, stride/steps feel.

## Tips

Wet floors, marble halls, puddles. Offscreen content cannot reflect — frame composition matters.

## Cost

High. Roughness cutoff trims the cost fastest.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
