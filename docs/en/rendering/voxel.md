# Voxel Rendering

Blocky reconstruction and volumetric experiments: CPU voxelization plus raymarched object effects and a voxel pass in the renderer.

Entries: `voxelize` and `voxel_raymarch` object effects (dedicated pages under Object Effects) with the CPU-side `voxel_cpu` helper preparing volumes. The renderer voxel pass composites blocky media consistently with scene depth.

Workflows: destructionChunks — voxelize effect driven 0→1 by a damage script; retro terrain accents — voxelized rocks alongside smooth meshes for style contrast; volumetric fog blocks — raymarched volumes for stylized smoke.

Like splats, voxels share scene collection and overlays but bypass parts of PostFX — check the final look in the game view. Voxel density is the cost knob: halve density for 8× fewer cells.

Troubleshooting: chunky in a bad way → density too low for the object size; seams → volume bounds smaller than the mesh; no effect in game view → PostFX chain ordering, verify pass order.

Related: Voxelize, Voxel Raymarch, Gaussian Splat Renderer, Object Effects (Overview).
