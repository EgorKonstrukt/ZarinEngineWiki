# Voxelize

Per-object effect through the ObjectFx pass (unlike PostFX, which processes the whole frame). Built on the shared ObjectEffect base; scripts drive it by animating progress/intensity floats.

## How it works

Rebuilds the object as chunky voxels with CPU-side preparation and a progress control.

## Parameters

Progress, voxel size.

## Tips

Retro transforms, build-up effects, destruction previews.

## Cost

Medium by voxel count.

Related: [Object Effects](../effects.md), Materials, Script Examples.
