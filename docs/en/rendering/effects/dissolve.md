# Dissolve

Per-object effect through the ObjectFx pass (unlike PostFX, which processes the whole frame). Built on the shared ObjectEffect base; scripts drive it by animating progress/intensity floats.

## How it works

Burns the surface away along animated noise: pixels past the threshold discard with an emissive rim at the edge.

## Parameters

Progress 0→1, edge width/color, noise scale.

## Tips

Spawns, deaths, portals, burns. Drive progress from a damage or timer script.

## Cost

Cheap: one noise sample plus discard.

Related: [Object Effects](../effects.md), Materials, Script Examples.
