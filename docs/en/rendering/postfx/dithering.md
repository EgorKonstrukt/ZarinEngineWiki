# Dithering

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Adds ordered noise that breaks up color banding in smooth gradients (skies, fog, dark interiors).

## Parameters

Strength, pattern scale.

## Tips

Always pair with fog and dark gradients; invisible at right strength, banding without it.

## Cost

Negligible.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
