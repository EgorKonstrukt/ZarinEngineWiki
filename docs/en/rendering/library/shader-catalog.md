# Shader Catalog

> Complete listing of every shader source under `core/shaders/` (`materials/`, `internal/`, `compute/`, `legacy/`, `include/`), with one-line purpose per file and verbatim `Properties` heads for the main shaders (PBR, Default, Sky, Water, Clouds).

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Shader sources live in `core/shaders/` and are compiled by `ShaderManager` (`core/renderer/shaderp/manager.py`) with fallback (`shaderp/fallback.py:110`). Counts at time of writing: `materials/` 9 files, `internal/` 22 files, `compute/` 11 files, `legacy/` 7 files, `include/` 2 files. Material shaders declare `Shader "Zarin/<Name>"` with a `Properties` block; compute shaders declare `ComputeShader "Zarin/<Name>"`.

## Materials (`core/shaders/materials/`)

| file | shader name | purpose |
|---|---|---|
| `PBR.shader` | `Zarin/PBR` | Default physically-based opaque material (metallic/smoothness, emission, normal/occlusion/height, clear-coat, anisotropy, detail maps, subsurface, thin-film, transmission). |
| `Unlit.shader` | `Zarin/Unlit` | Unlit material without lighting evaluation. |
| `Sky.shader` | `Zarin/Sky` | Procedural sky dome (sun disc, night sky, stars, Milky Way, moon). |
| `Skybox.shader` | `Zarin/Skybox` | Cubemap skybox sampling. |
| `Clouds.shader` | `Zarin/Clouds` | Volumetric-style fullscreen clouds (coverage, density, scattering, silver lining). |
| `CloudLayer.shader` | `Zarin/CloudLayer` | Flat cloud layer variant. |
| `Water.shader` | `Zarin/Water` | Default water material and reference for custom water shaders (`RenderType Transparent`). |
| `WaterSim.shader` | `Zarin/WaterSim` | Water simulation variant. |
| `Tree.shader` | `Zarin/Tree` | Vegetation/tree material (wind bends via Tree Wind effect). |

## Internal (`core/shaders/internal/`)

| file | shader name | purpose |
|---|---|---|
| `Default.shader` | `Zarin/Default` | Engine fallback material (empty `Properties`, instancing + skinning vertex inputs). |
| `Sprite.shader` | `Zarin/Sprite` | 2D sprite quad rendering. |
| `Video.shader` | `Zarin/Video` | Video quad rendering (`VideoRenderer`). |
| `Text.shader` | `Zarin/Text` | Signed-distance text quad rendering (`TextRenderer`). |
| `Particle.shader` | `Zarin/Particle` | CPU particle billboards. |
| `ParticleGpu.shader` | `Zarin/ParticleGpu` | GPU particle billboards. |
| `GaussianSplat.shader` | `Zarin/GaussianSplat` | Gaussian splat rasterization (needs OpenGL 4.3+). |
| `Shadow.shader` | `Zarin/Shadow` | Shadow-map depth pass. |
| `ShadowDebug.shader` | `Zarin/ShadowDebug` | Shadow-map debug visualization. |
| `ShadowOverlay.shader` | `Zarin/ShadowOverlay` | Shadow overlay compositing. |
| `Projector.shader` | `Zarin/Projector` | Light projector (`Projector` component). |
| `Outline.shader` | `Zarin/Outline` | Selection outline (wireframe overlay pass). |
| `ObjectFx.shader` | `Zarin/ObjectFx` | Object-effect injection target (dissolve/hologram/etc. snippets). |
| `Icon.shader` | `Zarin/Icon` | Editor billboard icons. |
| `Gizmo.shader` | `Zarin/Gizmo` | Editor gizmo lines. |
| `GizmoSolid.shader` | `Zarin/GizmoSolid` | Editor solid gizmo meshes. |
| `GizmoInstLine.shader` | `Zarin/GizmoInstLine` | Instanced gizmo lines. |
| `Grid.shader` | `Zarin/Grid` | Editor ground grid. |
| `GridOverlay.shader` | `Zarin/GridOverlay` | Grid overlay compositing. |
| `SkyboxLegacy.shader` | `Zarin/SkyboxLegacy` | Legacy skybox path. |
| `Caustics.shader` | `Zarin/Caustics` | Water caustics overlay. |
| `Underwater.shader` | `Zarin/Underwater` | Underwater fullscreen tint/fog. |

## Compute (`core/shaders/compute/`)

| file | shader name | purpose |
|---|---|---|
| `Raytracing.compute` | `Zarin/Raytracing` | Path-tracing kernel (`MAX_INSTANCES 256`, `MAX_LIGHTS 8`). |
| `SkyEnv.compute` | `Zarin/SkyEnv` | Sky environment map baking. |
| `Atmosphere.compute` | `Zarin/Atmosphere` | Atmospheric scattering bake. |
| `RadianceCascades.compute` | `Zarin/RadianceCascades` | Radiance-cascade GI probe pass. |
| `Cull.compute` | `Zarin/Cull` | GPU frustum/occlusion culling. |
| `BVHDebug.compute` | `Zarin/BVHDebug` | BVH debug visualization. |
| `particle.compute` | `Zarin/ParticleSim` | GPU particle simulation. |
| `Tree.compute` | `Zarin/TreeLeafSim` | Tree leaf simulation. |
| `terrain_gen.compute` | `TerrainGenerator` | Procedural terrain generation. |
| `shadow_cascade.compute` | — | Cascade shadow helper (no `ComputeShader` header; GLSL entry only). |
| `NavPathfinding.compute` | `Zarin/NavPathfinding` | GPU nav-mesh pathfinding. |

## Legacy (`core/shaders/legacy/`) and includes (`core/shaders/include/`)

| file | purpose |
|---|---|
| `legacy/voxelize.comp` | Legacy voxelization compute entry. |
| `legacy/transform.comp` | Legacy transform compute entry. |
| `legacy/skinning.comp` | Legacy skinning compute entry. |
| `legacy/cull.comp` | Legacy culling compute entry. |
| `legacy/aabb.comp` | Legacy AABB compute entry. |
| `legacy/Tree.gshader` | Legacy tree geometry shader. |
| `legacy/object_fx.geom` | Legacy object-FX geometry shader. |
| `include/caustics.glsl` | Shared caustics GLSL chunk. |
| `include/area_shadows.glsl` | Shared area-shadow GLSL chunk. |

## Key Properties blocks (verbatim)

`core/shaders/materials/PBR.shader:9-16` (head):

```c
Properties
{
    [MainColor] _BaseColor("Base Color", Color) = (1, 1, 1, 1)
    [MainTexture] _BaseMap("Base Map", 2D) = "white" {}
    _Metallic("Metallic", Range(0, 1)) = 0
    _Smoothness("Smoothness", Range(0, 1)) = 0.5
    _EmissionColor("Emission Color", Color) = (0, 0, 0, 0)
    _EmissionIntensity("Emission Intensity", Float) = 0
```

`core/shaders/internal/Default.shader:9-11` (full block):

```c
Properties
{
}
```

`core/shaders/materials/Sky.shader:9-16` (head):

```c
Properties
{
    _SunDirection("Sun Direction", Vector) = (0, -0.3, -1, 0)
    _SunColor("Sun Color", Color) = (1, 0.95, 0.85, 1)
    _SunIntensity("Sun Intensity", Float) = 1
    _SunAngularRadius("Sun Angular Radius (deg)", Float) = 0.27
    _SunLimbDarkening("Sun Limb Darkening", Range(0, 1)) = 0.7
    _SunConvergence("Sun Edge Softness", Range(0, 1)) = 0.5
```

`core/shaders/materials/Water.shader:68-75` (head):

```c
Properties
{
    _DeepColor("Deep Color", Color) = (0.015, 0.13, 0.22, 1)
    _ShallowColor("Shallow Color", Color) = (0.13, 0.46, 0.52, 1)
    _FoamColor("Foam Color", Color) = (0.92, 0.97, 1.0, 1)
    _SSSColor("SSS Color", Color) = (0.0, 0.55, 0.45, 1)
    _HorizonColor("Horizon Color", Color) = (0.62, 0.78, 0.86, 1)
    _Smoothness("Smoothness", Float) = 0.92
```

`core/shaders/materials/Clouds.shader:9-16` (head):

```c
Properties
{
    _Coverage("Cloud Coverage", Range(0, 1)) = 0.5
    _Density("Cloud Density", Float) = 1.0
    _Speed("Wind Speed", Float) = 0.1
    _WindDirection("Wind Direction", Float) = 18.0
    _Height("Cloud Height", Float) = 50.0
    _Thickness("Cloud Thickness", Float) = 20.0
```

## Usage in Code

```python
prog = shaders.get_or_compile("core/shaders/materials/Water.shader")
```

## Related pages

- [Shaders](../shaders.md), [Materials](../materials.md), [Pipeline](../pipeline.md), [Water](../water.md), [Raytracing Renderer](../raytracing-renderer.md)

## Verified against

- `core/shaders/materials/` — 9 `.shader` files, `Shader "..."` headers at `PBR:7`, `Sky:7`, `Water:66`, `Clouds:7`
- `core/shaders/internal/` — 22 `.shader` files, `Shader "..."` headers (each `:7`)
- `core/shaders/compute/` — 11 files, `ComputeShader "..."` headers (`Raytracing.compute:1`, others `:7`, except `shadow_cascade.compute`)
- `core/shaders/legacy/` — 7 files; `core/shaders/include/` — `caustics.glsl`, `area_shadows.glsl`
- `core/shaders/materials/PBR.shader:9-41`, `internal/Default.shader:7-15`, `materials/Sky.shader:9-36`, `materials/Water.shader:66-93`, `materials/Clouds.shader:9-32`
- `core/renderer/shaderp/manager.py`, `core/renderer/shaderp/fallback.py:110`
