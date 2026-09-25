# ModernGL Pipeline and Scene Renderer

Contexts:

- Game and editor viewports create the context with `moderngl.create_context(standalone=False)` — a shared 4.6 core context inside the Qt surface.
- Offscreen work (previews, terrain workers, compute runners) uses `moderngl.create_standalone_context(require=430)`.
- Shaders are authored as `#version 430 core`. If compilation fails, the loader retries as `#version 330 core`, stripping `layout(std430)` buffers and forcing instancing/skinning off — a compatibility fallback, not a second code path to maintain.

The `Renderer` class is composed from mixins: programs, uniforms, framebuffers, scene collection, geometry cache, main scene pass, shadow pass, cubemap pass, skinned pass, water/voxel/object-FX passes, post pass, gizmo/icon/outline passes, culling and stats.

A frame walks these stages:

1. Collect visible render items from the scene (frustum culling, layer masks).
2. Render shadow maps (cascaded directional, point cube, spot) into depth targets.
3. Render the main scene with bound materials, lights (up to 8 by default) and ambient.
4. Run water, voxel and object-FX passes into their targets.
5. Run the PostFX chain.
6. Draw editor overlays (grid, gizmos, icons, outlines, selection) — editor viewports only.

Render modes: `SHADED`, `SHADED_WIREFRAME`, `FLAT`. Default clear color is dark grey `[0.18, 0.18, 0.18]`, default ambient is cool `[0.26, 0.28, 0.34]`.
