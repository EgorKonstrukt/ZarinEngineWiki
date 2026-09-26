# Chromatic Aberration

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Samples RGB channels with tiny radial offsets growing toward the frame edges.

## Parameters

Intensity, center, edge falloff.

## Tips

Damage feedback, cheap camera character, dream sequences. Subtle values sell lenses; high values sell injury.

## Cost

Cheap: three offset taps.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
