# Voxel Raymarch

Per-object effect through the ObjectFx pass (unlike PostFX, which processes the whole frame). Built on the shared ObjectEffect base; scripts drive it by animating progress/intensity floats.

## How it works

Raymarched voxel volume rendering for dense blocky media with depth-consistent compositing.

## Parameters

Density, step count, tint.

## Tips

Stylized smoke, volumetric magic, blocky clouds.

## Cost

High by step count. Halve steps first.

Related: [Object Effects](../effects.md), Materials, Script Examples.
