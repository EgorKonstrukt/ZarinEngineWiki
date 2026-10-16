# Рейтрейсинг-рендерер
> Compute path-tracer оверлей: `RaytraceComponent` (`core/components/rendering/renderers/raytrace/component.py:23-161`), шейдер по умолчанию `core/shaders/compute/Raytracing.compute`.
[Home](../../index.md) · [Section index](./_section.md)
## Overview
Лимиты: `_MAX_INSTANCES = 256`, `_MAX_LIGHTS = 8`, `_MAT_STRIDE = 20`, `_MAX_ALBEDO_LAYERS = 32`, `_INST_STRIDE = 46` (`component.py:13-17`). Опциональная Cython-упаковка через `core._raytracing_data.prepare_raytrace_data`. Буферы (BVH/verts/idx/mats/instances/lights + albedo array) освобождаются при disable/destroy (`component.py:123-161`).
## Properties
| name | type | default | description |
|---|---|---|---|
| `enabled` | bool | `True` | Мастер-тумблер (`component.py:29`). |
| `_compute_shader_path` | путь (*.compute) | `core/shaders/compute/Raytracing.compute` | Compute-шейдер (`component.py:30,42`). |
| `_resolution_scale` | float (0.1–1) | `0.5` | Разрешение трейса (`component.py:31,43`). |
| `_max_bounces` | int (1–16) | `1` | Отскоки (`component.py:32,44`). |
| `_samples_per_pixel` | int (1–16) | `1` | SPP (`component.py:33,45`). |
| `_accumulate` | bool | `False` | Накопление кадров (`component.py:34,46`). |
| `_show_overlay` | bool | `True` | Композит оверлея (`component.py:35,47`). |
| `_heatmap_mode` | int (0–2) | `0` | Режим хитмапа (`component.py:37,48`). |
| `_heatmap_scale` | float (1–256) | `40.0` | Масштаб хитмапа (`component.py:38,49`). |
## Usage in Editor
Компонент на превью-сущность, Resolution Scale 0.25–0.5 для интерактива, Accumulate для сошедшихся still-кадров.
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
