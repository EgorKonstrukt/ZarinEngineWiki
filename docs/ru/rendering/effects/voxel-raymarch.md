# Воксельный реймарш

> Меш→объём реймарш. Хост-меш вокселизуется на CPU в 3D-текстуру (dims кламп до 192), затем полноэкранный проход реймаршит (max_steps) с альфой типа Beer-Lambert × opacity × amount, шейдинг — color/emission/rim и light_dir. Бленд SRC_ALPHA/ONE_MINUS_SRC_ALPHA поверх цели цвета сцены. Класс `VoxelRaymarchEffect` в `core/components/rendering/effects/voxelp/effect.py`. NOTE: despite living under effects/, this is a GraphicsEffect (fullscreen raymarch into a 3D occupancy texture), not an ObjectEffect. Requires a MeshFilter on the same entity to build the grid; disabled/fast-pathed when amount/opacity ≤ 0.001.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Voxel Size | `voxel_size` | FLOAT | — | `0.3` |
| Voxel Color | `color` | COLOR | — | `[0.4, 1.0, 0.6]` |
| Glow | `emission` | FLOAT | — | `0.6` |
| Rim Power | `rim` | FLOAT | — | `2.5` |
| Opacity | `opacity` | SLIDER | 0.0 – 1.0 | `1.0` |
| Max Steps | `max_steps` | INT | 32 – 1024 | `256` |
| Light Dir | `light_dir` | VEC3 | — | `(0.4, 0.8, 0.3)` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `VoxelRaymarchEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.voxelp.effect import VoxelRaymarchEffect
fx = VoxelRaymarchEffect()
entity.add_component(fx)
fx.voxel_size = 0.2
fx.max_steps = 128
fx.opacity = 0.9
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/components/rendering/effects/voxelp/shaders.py (VR_VERT / VR_FRAG)`. Привязанные uniform-ы: `u_inv_view_proj`, `u_cam_pos`, `u_grid_min`, `u_grid_dims`, `u_cell_size`, `u_vox_color`, `u_vox_emission`, `u_vox_rim`, `u_amount`, `u_opacity`, `u_light_dir`, `u_max_steps`, `u_grid`, `u_depth_tex`.

```glsl
// VR_FRAG uniforms (exact names from shaders.py):
// uniform sampler3D u_grid; uniform vec3 u_grid_min; uniform vec3 u_grid_dims; uniform float u_cell_size;
// uniform mat4 u_inv_view_proj; uniform vec3 u_cam_pos; uniform sampler2D u_depth_tex;
// uniform vec3 u_vox_color; uniform float u_vox_emission; uniform float u_vox_rim;
// uniform float u_amount; uniform float u_opacity; uniform vec3 u_light_dir; uniform int u_max_steps;
```

## Связанное

См. [Эффекты объектов](../effects.md), [Вокселизация](voxelize.md), `core/components/rendering/effects/voxelp/shaders.py`, `core/components/rendering/effects/voxel_cpu.py`.

## Проверено по коду

- `core/components/rendering/effects/voxelp/effect.py` — класс `VoxelRaymarchEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `_intensity_prop = "amount"`, сигнатура `render()`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
