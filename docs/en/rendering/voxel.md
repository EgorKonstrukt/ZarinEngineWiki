# Voxel Rendering

Blocky reconstruction and volumetric experiments: CPU voxelization plus raymarched object effects and a voxel pass in the renderer.

Entries: `voxelize` and `voxel_raymarch` object effects (per-object pages under Object Effects), with a CPU-side `voxel_cpu` helper.

Like splats, voxels share scene collection and overlays but bypass parts of PostFX — check the final look in the game view.

Related: Voxelize, Voxel Raymarch, Gaussian Splat Renderer.
