# Глубина резкости

> Размытие по глубине. Circle-of-confusion от focal distance/range, aperture и blur size; сборка Gaussian или полигональное Bokeh (blade count / curvature / rotation). Небо (depth >= 1.0) считается полностью размытым фоном. Класс `DepthOfField` в `core/components/rendering/postfx/depth_of_field.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Mode | `_mode` | ENUM DoFMode {Gaussian, Bokeh} | — | `Gaussian` |
| Focal Distance | `_focal_distance` | SLIDER | 0.1 – 200.0 | `10.0` |
| Focal Range | `_focal_range` | SLIDER | 0.001 – 100.0 | `8.0` |
| Aperture | `_aperture` | SLIDER | 0.0 – 5.0 | `1.0` |
| Max Blur Size | `_max_blur_size` | SLIDER | 1.0 – 50.0 | `12.0` |
| Bokeh Highlight Boost | `_bokeh_boost` | SLIDER | 0.0 – 3.0 | `0.5` |
| Foreground Blur Scale | `_foreground_scale` | SLIDER | 1.0 – 5.0 | `2.0` |
| Rings | `_ring_count` | INT_SLIDER | 1 – 5 | `3` |
| Blade Count | `_blade_count` | INT_SLIDER | 3 – 9 | `6` |
| Curvature | `_blade_curvature` | SLIDER | 0.0 – 1.0 | `1.0` |
| Rotation | `_blade_rotation` | SLIDER | 0.0 – 360.0 | `0.0` |
| Visualize CoC | `_visualize_coc` | BOOL | — | `False` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `DepthOfField`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.depth_of_field import DepthOfField, DoFMode
fx = DepthOfField()
entity.add_component(fx)
fx._mode = DoFMode.BOKEH
fx._focal_distance = 12.0
fx._aperture = 1.5
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/depth_of_field.py (DOF_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_depth_tex;
uniform mat4 u_inv_proj;
uniform float u_focal_distance;
uniform float u_focal_range;
uniform float u_aperture;
uniform int u_mode;  // 0 Gaussian, 1 Bokeh
```


## Производительность

Cost scales with Rings (1–5, inner loop r*8 taps, up to 64 per ring) × Max Blur Size. Pixels with |CoC| < 0.5 early-out.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/depth_of_field.py` — класс `DepthOfField`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen", _skip_rate = 0`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
