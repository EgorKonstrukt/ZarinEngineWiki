# Bloom

Part of the PostFX stack: runs after the main scene pass in component order. Built on the shared GraphicsEffect base with an enable toggle plus effect-specific Inspector fields.

## How it works

Bright pixels above a threshold bleed into neighbours through a downsampled blur pyramid, then add back over the frame.

## Parameters

Threshold, intensity, radius/softness, tint.

## Tips

Pair with Emissive Pulse fixtures and sun-lit metal; keep threshold above the sky or the whole sky blooms.

## Cost

Medium: pyramid taps scale with radius. One bloom is fine; two is a smell.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
