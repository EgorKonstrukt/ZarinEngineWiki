# SSR

> Экранные отражения. Реймарш во view-space (steps/thickness/max distance) с затуханием к краям экрана и дистанции плюс френелевское взвешивание (power/strength); результат композитится поверх кадра по intensity. Класс `SSR` в `core/components/rendering/postfx/ssr.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | SLIDER | 0.0 – 2.0 | `0.5` |
| Steps | `_steps` | INT_SLIDER | 8 – 64 | `32` |
| Thickness | `_thickness` | SLIDER | 0.05 – 4.0 | `1.5` |
| Max Distance | `_max_distance` | SLIDER | 10.0 – 200.0 | `60.0` |
| Edge Fade | `_edge_fade` | SLIDER | 0.02 – 0.3 | `0.12` |
| Distance Fade | `_distance_fade` | SLIDER | 0.3 – 0.95 | `0.6` |
| Fresnel Power | `_fresnel_power` | SLIDER | 1.0 – 8.0 | `4.0` |
| Fresnel Strength | `_fresnel_strength` | SLIDER | 0.0 – 1.0 | `0.05` |
| Resolution Scale | `_performance_scale` | SLIDER | 0.25 – 1.0 | `0.5` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `SSR`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.ssr import SSR
fx = SSR()
entity.add_component(fx)
fx._steps = 48
fx._max_distance = 80.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/ssr.py`.

```glsl
#version 330 core
uniform sampler2D u_depth_tex;
uniform sampler2D u_input_tex;
uniform mat4 u_proj;
uniform mat4 u_inv_proj;
uniform vec2 u_pixel_size;
// + u_steps, u_thickness, u_max_distance, u_edge_fade, u_distance_fade, u_fresnel_power, u_fresnel_strength, u_intensity
```


## Производительность

Cost scales with Steps (8–64) × (Resolution Scale)² (default 0.5).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/ssr.py` — класс `SSR`, `_inspector_fields`, дефолты `__init__`, `Depth raymarch + composite. Seeded kernel not used; uses proj/inv_proj + pixel size.`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
