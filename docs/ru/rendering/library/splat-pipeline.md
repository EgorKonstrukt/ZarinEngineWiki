# Пайплайн сплатов

> Gaussian splat ingest → загрузка на GPU → отсортированная растеризация: поддерживаемые форматы, SH degree, cutoff прозрачности, требования GL и сортировщик `_splat_sort`.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

`GaussianSplatRenderer` (`core/components/rendering/renderers/gaussian_splat_renderer.py:12-35`) указывает на файл сплатов и настраивает вычисление. GPU-бэкенд — `_SplatResourcesMixin` (`core/renderer/splat/resources.py:330-388`); сортировка — опциональный Cython-модуль `core._splat_sort` (`core/renderer/splat/sort.py:31-34`, сборка в `core/build_cython.py:127`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `ply_path` | resource path | `""` | Файл сплатов; фильтр пикера `Splats (*.ply *.sog *.ssog)` (`gaussian_splat_renderer.py:23-24`). |
| `sh_degree` | int-слайдер 0–3 | `3` | Degree сферических гармоник, уходит в `u_sh_degree` (`gaussian_splat_renderer.py:25-26`, `resources.py:385-386`). |
| `opacity_threshold` | float 0–1 | `0.005` | Cutoff прозрачности, уходит в `u_opacity_threshold` (`gaussian_splat_renderer.py:27-28`, `resources.py:387-388`). |

## Форматы и импорт

`_is_sog_style` (`resources.py:306-313`) считает директориально-архивными сплатами: `.sog`, `.ssog`, `.zip`, пути на `/meta.json` или `/lod-meta.json`, либо basename `meta.json` / `lod-meta.json`. Остальное идёт через `_load_splat_file` (`resources.py:316-327`): первые 4 строки сниффятся — `binary` PLY идёт быстрым путём (`_load_splat_fast`), иначе generic-лоадер (`_load_splat_generic`). Относительный `ply_path` резолвится от корня проекта с фолбэком в `assets/` (`post_pass.py:47-61`).

## Требования GPU и сортировка

- Падение инициализации шейдера логирует `GaussianSplatRenderer shader init failed (needs OpenGL 4.3+): ...` и отключает программу/VAO (`resources.py:342-345`). Используются SSBO storage-буферы (`_ensure_buffers`, `_bind_gpu_draw` на биндингах 0–1).
- Покадровые юниформы: `u_model`, `u_view`, `u_proj`, `u_viewport`, `u_camera_pos`, `u_sh_degree`, `u_opacity_threshold` (`resources.py:369-388`).
- Сортировка — `splat_cull_depth`, `compact_keep`, `radix_sort_into`, `remap_order` из `core._splat_sort` когда скомпилирован, иначе NumPy-фолбэк (`splat/sort.py:31-34,168`).

## Usage in Code

```python
from core.components.rendering.renderers.gaussian_splat_renderer import GaussianSplatRenderer
gs = GaussianSplatRenderer()
gs.ply_path = "assets/splats/scan.ply"
gs.sh_degree = 3
gs.opacity_threshold = 0.005
entity.add_component(gs)
```

## Related pages

- [Gaussian Splat Renderer](../gaussian-splat-renderer.md), [Конвейер](../pipeline.md)

## Verified against

- `core/components/rendering/renderers/gaussian_splat_renderer.py:19-53` (поля, дефолты, сериализация)
- `core/renderer/splat/resources.py:300-388` (детект SOG, диспетчер лоадеров, сообщение GL 4.3+ в `:343`, SSBO-буферы, юниформы)
- `core/renderer/splat/sort.py:31-34`, `core/build_cython.py:127` (`core._splat_sort`)
- `core/renderer/post_pass.py:47-61` (резолв `_gaussian_ply_path`)
