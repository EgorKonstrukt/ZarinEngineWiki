# Настройка рейтрейсинга

> Контракт ядра `Raytracing.compute`, ручки компонента, жёсткие лимиты и гайд по производительности.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Паттрейсер — compute-ядро `#version 430 core` (`local_size 8×8`, `core/shaders/compute/Raytracing.compute:13-15`), управляемое компонентом `RaytraceRenderer` из `core/components/rendering/renderers/raytrace/` (`component.py`, `dispatch.py`, `denoise.py`).

## Свойства ядра (verbatim, `Raytracing.compute:3-11`)

```c
Properties
{
    _MaxBounces("Max Bounces", Int) = 1
    _SamplesPerPixel("Samples Per Pixel", Int) = 1
    _ResolutionScale("Resolution Scale", Float) = 1.0
    _Accumulate("Accumulate Frames", Int) = 0
    _HeatmapMode("Heatmap Mode", Int) = 0
    _HeatmapScale("Heatmap Scale", Float) = 40.0
}
```

Жёсткие GLSL-лимиты (`Raytracing.compute:17-18`): `#define MAX_INSTANCES 256`, `#define MAX_LIGHTS 8`. Лишние инстансы/свет дропаются при загрузке сцены (`denoise.py:85,217-243`).

## Ручки компонента (`raytrace/component.py:31-49`)

| field | range | default | description |
|---|---|---|---|
| `_resolution_scale` | 0.1–1.0 | `0.5` | Фактор разрешения трейса; размер диспатча `max(1, size × scale)` (`dispatch.py:17-18`). |
| `_max_bounces` | 1–16 | `1` | → `u_max_bounces`. |
| `_samples_per_pixel` | 1–16 | `1` | → `u_samples_per_pixel`. |
| `_accumulate` | bool | `False` | → `u_accumulate`; прогрессивное уточнение (`dispatch.py:126`). |
| `_show_overlay` | bool | `True` | Показ композитного оверлея. |
| `_heatmap_mode` | 0–2 | `0` | → `u_heatmap_mode` (выкл/диагностика стоимости). |
| `_heatmap_scale` | 1–256 | `40.0` | → `u_heatmap_scale`. |

Буферы диспатча: BVH-узлы (0), вершины (1), индексы (2), материалы (3), инстансы (4), свет (5), плюс `rgba32f` output/emissive-изображения и 32-слойный массив albedo-текстур (`_MAX_ALBEDO_LAYERS = 32`, `component.py:17`, `dispatch.py:79`).

## Требования

- OpenGL 4.3+ compute (SSBO + image load/store, `#version 430 core`).
- Лимиты сцены: 256 инстансов, 8 светов, 32 albedo-слоя — компоненты сверх лимита молча игнорируются.
- Учёт лучей/кадр: `rw × rh × spp × (bounces + 1)` (`dispatch.py:271`, наружу как `_rt_rays_per_frame`).

## Заметки по производительности

Стоимость линейна от пикселей (× `resolution_scale²`), spp и bounces+1. Старт: `0.5` scale / 1 bounce / 1 spp, для статиков включайте `_accumulate`, а перед повышением качества смотрите `_heatmap_mode` для поиска хотспотов.

## Usage in Code

```python
rt._resolution_scale = 0.5
rt._max_bounces = 1
rt._samples_per_pixel = 1
rt._accumulate = False
```

## Related pages

- [Raytracing Renderer](../raytracing-renderer.md), [Каталог шейдеров](./shader-catalog.md), [Конвейер](../pipeline.md)

## Verified against

- `core/shaders/compute/Raytracing.compute:1-60` (Properties, `#version 430`, `local_size 8×8`, `MAX_INSTANCES 256`, `MAX_LIGHTS 8`, юниформы)
- `core/components/rendering/renderers/raytrace/component.py:14-49,95-121` (лимиты, ручки, дефолты, сериализация)
- `core/components/rendering/renderers/raytrace/dispatch.py:17-18,124-160,271-294` (scale, юниформы, счёт лучей, оверлей)
- `core/components/rendering/renderers/raytrace/denoise.py:85,217-260` (применение лимитов)
