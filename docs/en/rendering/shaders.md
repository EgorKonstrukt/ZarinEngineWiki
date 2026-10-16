# Shaders
> Shader asset: `.shader` text with `Shader "Name" { Properties { ... } SubShader { Pass { GLSLPROGRAM ... } } }`, compiled by `ShaderManager` (`core/renderer/shaderp/manager.py`) with fallback (`fallback.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Built-ins in `core/shaders/materials/`: `PBR`, `Unlit`, `Sky`, `Skybox`, `Clouds`, `CloudLayer`, `Water`, `WaterSim`, `Tree`. Compute in `core/shaders/compute/` (raytracing). Shaders requiring OpenGL 4.3+ storage buffers warn and fall back (`shaderp/fallback.py:110`). PBR vertex inputs include instancing (`in_model0-3`, `in_color`) + skinning (`in_bone_indices/weights`, SSBO `u_bone_matrices`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `Properties` block | declaration list | per-shader | Exposed uniforms parsed for the inspector/materials. |
| `SubShader.Tags.RenderType` | string | `"Opaque"` | Opaque/transparent routing (e.g. PBR `Tags { "RenderType" = "Opaque" }`). |
| `GLSLPROGRAM` | GLSL `#version 430 core` | per-shader | Vertex/fragment bodies. |
## Usage in Editor
Reference by path in `material_path` / `Material.shader_path` / component `shader` fields. Missing shaders fall back with a warning — check the log.
## Usage in Code
```python
prog = shaders.get_or_compile("core/shaders/materials/Water.shader")
```
## Material/Shader snippet
`core/shaders/materials/PBR.shader` Properties (verbatim head):
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
## Related pages
- [Materials](./materials.md), [Pipeline](./pipeline.md), [Mesh Renderer](./mesh-renderer.md), [Water](./water.md)
## Verified against
- `core/shaders/materials/PBR.shader:7-45`
- `core/renderer/shaderp/manager.py`, `core/renderer/shaderp/fallback.py:110`
- `core/renderer/mats/shader.py`
