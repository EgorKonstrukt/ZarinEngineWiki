# Туман

> Дистанционный + высотный туман с солнечным рассеянием и дымкой горизонта. Мировая позиция восстанавливается из depth-буфера; density/start — дистанция, height — приземный слой, sun tint/intensity — рассеяние к солнцу, sky fade/horizon — переход в небо. Класс `Fog` в `core/components/rendering/postfx/fog.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Color | `_fog_color` | COLOR | — | `(0.65, 0.72, 0.82)` |
| Density | `_density` | SLIDER | 0.0 – 0.2 | `0.004` |
| Start Distance | `_start_distance` | FLOAT | 0.0 – 500.0 | `0.0` |
| Height Density | `_height_density` | SLIDER | 0.0 – 2.0 | `0.0` |
| Base Height | `_height` | FLOAT | -100.0 – 500.0 | `0.0` |
| Sun Tint | `_sun_color` | COLOR | — | `(1.0, 0.9, 0.75)` |
| Sun Intensity | `_sun_intensity` | SLIDER | 0.0 – 2.0 | `0.25` |
| Sun Light | `_sun_light_entity_id` | GAMEOBJECT entity id | — | `""` |
| Fade To Sky | `_sky_fade` | BOOL | — | `True` |
| Horizon Haze | `_sky_horizon` | SLIDER | 0.0 – 1.0 | `0.5` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Fog`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.fog import Fog
fx = Fog()
entity.add_component(fx)
fx._density = 0.01
fx._sun_light_entity_id = sun_entity.id
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/fog.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_depth_tex;
uniform mat4 u_inv_proj;
uniform mat4 u_inv_view;
uniform vec3 u_camera_pos;
// + u_density, u_start, u_height, u_height_density, u_fog_color, u_sun_color, u_sun_dir, u_sun_intensity, u_sky_fade, u_sky_horizon, u_far
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/fog.py` — класс `Fog`, `_inspector_fields`, дефолты `__init__`, `Depth-reconstruction pass (u_depth_tex + u_inv_proj/u_inv_view + u_camera_pos)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
