# Raytracing Renderer
> Compute-shader path tracer overlay: `RaytraceComponent` (`core/components/rendering/renderers/raytrace/component.py:23-161`), default shader `core/shaders/compute/Raytracing.compute`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Limits: `_MAX_INSTANCES = 256`, `_MAX_LIGHTS = 8`, `_MAT_STRIDE = 20`, `_MAX_ALBEDO_LAYERS = 32`, `_INST_STRIDE = 46` (`component.py:13-17`). Optional Cython packing via `core._raytracing_data.prepare_raytrace_data`. Buffers (BVH/verts/idx/mats/instances/lights + albedo array) are released on disable/destroy (`component.py:123-161`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `enabled` | bool | `True` | Master toggle (`component.py:29`). |
| `_compute_shader_path` | resource path (*.compute) | `core/shaders/compute/Raytracing.compute` | Compute shader (`component.py:30,42`). |
| `_resolution_scale` | float (0.1–1) | `0.5` | Trace resolution (`component.py:31,43`). |
| `_max_bounces` | int (1–16) | `1` | Bounces (`component.py:32,44`). |
| `_samples_per_pixel` | int (1–16) | `1` | SPP (`component.py:33,45`). |
| `_accumulate` | bool | `False` | Temporal accumulation (`component.py:34,46`). |
| `_show_overlay` | bool | `True` | Composite overlay (`component.py:35,47`). |
| `_heatmap_mode` | int (0–2) | `0` | Heatmap mode (`component.py:37,48`). |
| `_heatmap_scale` | float (1–256) | `40.0` | Heatmap scale (`component.py:38,49`). |
## Usage in Editor
Add the component to a preview entity, lower Resolution Scale to 0.25–0.5 for interactivity, enable Accumulate for converged stills.
## Usage in Code
```python
rt._resolution_scale = 0.5
rt._samples_per_pixel = 2
rt._accumulate = True
```
## Related pages
- [Pipeline](./pipeline.md), [Materials](./materials.md), [Shaders](./shaders.md)
## Verified against
- `core/components/rendering/renderers/raytrace/component.py:13-50,91-122`
- `core/shaders/compute/Raytracing.compute`
