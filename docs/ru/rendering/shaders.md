# Шейдеры
> Ассет шейдера: текст `.shader` вида `Shader "Name" { Properties { ... } SubShader { Pass { GLSLPROGRAM ... } } }`, компилирует `ShaderManager` (`core/renderer/shaderp/manager.py`) с фолбэком (`fallback.py`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Встроенные в `core/shaders/materials/`: `PBR`, `Unlit`, `Sky`, `Skybox`, `Clouds`, `CloudLayer`, `Water`, `WaterSim`, `Tree`. Compute — в `core/shaders/compute/` (рейтрейсинг). Шейдеры, требующие storage-буферов OpenGL 4.3+, варнят и откатываются (`shaderp/fallback.py:110`). Вершина PBR включает инстансинг (`in_model0-3`, `in_color`) + скиннинг (`in_bone_indices/weights`, SSBO `u_bone_matrices`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `Properties` блок | список деклараций | по шейдеру | Открытые юниформы для инспектора/материалов. |
| `SubShader.Tags.RenderType` | string | `"Opaque"` | Маршрутизация opaque/transparent (у PBR `Tags { "RenderType" = "Opaque" }`). |
| `GLSLPROGRAM` | GLSL `#version 430 core` | по шейдеру | Тела vertex/fragment. |
## Usage in Editor
Ссылка путём в `material_path` / `Material.shader_path` / поле `shader` компонентов. Отсутствующие откатываются с варнингом — смотреть лог.
## Usage in Code
```python
prog = shaders.get_or_compile("core/shaders/materials/Water.shader")
```
## Material/Shader snippet
`core/shaders/materials/PBR.shader` Properties (дословная шапка):
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
