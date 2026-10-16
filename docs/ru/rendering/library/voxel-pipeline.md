# Воксельный пайплайн

> Вокселизация объектов (`VoxelizeEffect`), инстансинг-бэкенд `voxel_pass` с жёсткими лимитами и скринспейс `VoxelRaymarchEffect`.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Воксели покрывают два компонента: `VoxelizeEffect` (`ObjectEffect`, вокселизует хост-меш) и `VoxelRaymarchEffect` (`GraphicsEffect`, реймаршит 3D-текстуру occupancy). Инстансированные воксель-кубы рисует `VoxelPassMixin` (`core/renderer/voxel_pass.py:18-25`).

## VoxelizeEffect (`core/components/rendering/effects/voxelize_effect.py:18-52`)

| name | type | default | description |
|---|---|---|---|
| `amount` | слайдер 0–1 | `0.5` | Бленд базового меша и вокселей. |
| `show_base_mesh` | bool | `True` | Продолжать рисовать исходный меш. |
| `world_grid` | bool | `False` | Привязка вокселей к мировой сетке вместо объектной. |
| `voxel_size` | float | `0.3` | Целевой размер вокселя; также `cell = 0.3`. |
| `scale` | vec3 | `[1,1,1]` | Масштаб воксель-куба. |
| `jitter` | float | `0.0` | Фактор джиттера ячеек. |
| `emission` | float | `2.5` | Свечение вокселей. |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Тинт вокселей. |
| `rim` | float | `2.5` | Rim power. |
| `double_sided` | bool | `True` | Двусторонние воксель-кубы. |
| `animate` / `speed_anim` / `ping_pong` | bool / float / bool | `False` / `0.5` / `False` | Авто-анимация `amount` 0→1. |

Ячейки вокселей считает CPU-функция `compute_voxel_instances` с кэшем по ключу (меш, размер, сетка, джиттер) (`voxelize_effect.py:59-72`).

## Лимиты voxel_pass (`core/renderer/voxel_pass.py:22-25`)

| constant | value | meaning |
|---|---|---|
| `_VOX_MAX_DIM` | `128` | Макс. размерность сетки; большие экстенты рескейлят ячейку (`voxel_pass.py:304-309`). |
| `_VOX_MAX_INSTANCES` | `2200000` | Кламп инстансов: `need_inst = min(total, _VOX_MAX_INSTANCES)` (`voxel_pass.py:164`). |

Инстансированный вершинный шейдер (`_VOXEL_INST_VERT`, `#version 330 core`) пакует `i_cell.xyz` (центр) + `i_cell.w` (флаг каллинга) с `u_vox_size`/`u_vox_scale`.

## VoxelRaymarchEffect (`core/components/rendering/effects/voxelp/effect.py:22-59`)

`GraphicsEffect` (`render_type = "additive"`, `_intensity_prop = "amount"`, разрешены множественные) с полноэкранным реймаршем по `texture3d` occupancy-сетке, построенной из хостового `MeshFilter`.

| name | type | default | description |
|---|---|---|---|
| `amount` | слайдер 0–1 | `1.0` | Интенсивность (авто-скип при `<= 0.001`). |
| `voxel_size` | float | `0.3` | Размер ячейки сетки. |
| `color` | color3 | `[0.4, 1.0, 0.6]` | Цвет вокселей. |
| `emission` | float | `0.6` | Свечение. |
| `rim` | float | `2.5` | Rim power. |
| `opacity` | слайдер 0–1 | `1.0` | Итоговая прозрачность. |
| `max_steps` | int 32–1024 | `256` | Шаги реймарша. |
| `light_dir` | vec3 | `[0.4, 0.8, 0.3]` | Направление света марша. |

Лимит построения сетки: `max_dim = 192` — если `ceil(extent / cell)` превышает, ячейка укрупняется до `max(extent) / 192` (`effect.py:164-167`).

## Usage in Code

```python
from core.components.rendering.effects.voxelize_effect import VoxelizeEffect
vx = VoxelizeEffect()
vx.amount = 0.5
vx.voxel_size = 0.3
entity.add_component(vx)
```

## Related pages

- [Вокселизация](../effects/voxelize.md), [Воксельный реймарш](../effects/voxel-raymarch.md), [Воксельный рендеринг](../voxel.md), [Эффекты объектов](../effects.md)

## Verified against

- `core/components/rendering/effects/voxelize_effect.py:18-152` (дефолты, сериализатор, инспектор)
- `core/renderer/voxel_pass.py:18-25,164,304-309` (`_VOX_MAX_DIM = 128`, `_VOX_MAX_INSTANCES = 2200000`)
- `core/components/rendering/effects/voxelp/effect.py:22-91,156-185` (`VoxelRaymarchEffect`, `max_dim = 192`, `max_steps = 256`)
- `core/renderer/object_fx_pass.py:15,114` (зацеп voxel-эффекта)
