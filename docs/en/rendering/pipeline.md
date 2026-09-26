# ModernGL Pipeline and Scene Renderer

Contexts:

- Game and editor viewports create the context with `moderngl.create_context(standalone=False)` — a shared 4.6 core context inside the Qt surface, so editor and game views share textures and programs.
- Offscreen work (material/model previews, terrain workers, compute runners) uses `moderngl.create_standalone_context(require=430)` with no visible surface.
- Shaders are authored as `#version 430 core`. If compilation fails, the loader retries as `#version 330 core`, stripping `layout(std430)` buffers and forcing instancing/skinning off — a compatibility fallback, not a second code path to maintain.

The `Renderer` class is composed from mixins, one responsibility each: programs, uniforms, framebuffers, scene collection, geometry cache, main scene pass, shadow pass, cubemap pass, skinned pass, water/voxel/object-FX passes, post pass, gizmo/icon/outline passes, culling and stats. Read a stage by opening its mixin file under `core/renderer` (`shadows.py`, `scene_renderer.py`, `materials.py`, `framebuffers.py` and siblings).

A frame walks these stages:

1. Collect visible render items from the scene: frustum culling against camera planes, layer-mask filtering, sorting into opaque / transparent / overlay buckets.
2. Render shadow maps (cascaded directional, point cubes, spot perspectives) into depth targets; projector shadows join this pass.
3. Render the main scene with bound materials, up to 8 lights by default plus ambient, and per-material transparency handling.
4. Run water, voxel and object-FX passes into their targets.
5. Run the PostFX chain in component order.
6. Draw editor overlays (grid, gizmos, icons, outlines, selection) — editor viewports only.

Key defaults: `_max_lights = 8` with per-light type/position/direction/color/intensity/range/spot/area uniforms; `_ambient = [0.26, 0.28, 0.34]`; shadow resolution 2048, distance 50, 4 cascades; clear color dark grey `[0.18, 0.18, 0.18]`; line width 0.6667 for debug drawing. Render modes: `SHADED`, `SHADED_WIREFRAME`, `FLAT`.

Troubleshooting: black viewport → context creation failed (check OpenGL 4.6 support) or all lights culled; pink/missing materials → shader compile failed and fallback missed (see console, check Shaders page); z-fighting → push `near` out or pull `far` in on the camera; shadows absent → `cast_shadows` off or outside shadow distance.

Related: Cameras, Lights and Shadows, Materials, Shaders, Profiler (per-stage timings), Gizmos and Icons.
