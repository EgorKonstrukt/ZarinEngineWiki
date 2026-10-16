# SSAO

> Экранная окклюзия из глубины + проекции. Хемисферные сэмплы в радиусе сравниваются по глубине (bias/power/intensity формируют затемнение), затем edge-aware билатеральный блюр сглаживает, сохраняя разрывы глубины. Класс `SSAO` в `core/components/rendering/postfx/ssaop/component.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | SLIDER | 0.0 – 2.0 | `1.0` |
| Radius | `_radius` | SLIDER | 0.1 – 5.0 | `1.0` |
| Power | `_power` | SLIDER | 0.1 – 5.0 | `2.0` |
| Bias | `_bias` | SLIDER | 0.0 – 0.1 | `0.025` |
| Samples | `_kernel_size` | INT_SLIDER | 4 – 64 | `32` |
| Resolution Scale | `_performance_scale` | SLIDER | 0.25 – 1.0 | `0.5` |
| Blur Radius | `_bilateral_radius` | SLIDER | 1.0 – 6.0 | `3.0` |
| Edge Sensitivity | `_bilateral_sigma` | SLIDER | 1.0 – 200.0 | `50.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `SSAO`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.ssaop.component import SSAO
fx = SSAO()
entity.add_component(fx)
fx._radius = 1.5
fx._kernel_size = 32
fx._performance_scale = 0.5
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/ssaop/shaders.py`.

```glsl
// SSAO uniforms (exact names):
// uniform sampler2D u_depth_tex; uniform mat4 u_proj; uniform mat4 u_inv_proj;
// uniform vec2 u_pixel_size; uniform vec2 u_resolution;
// uniform float u_radius; uniform float u_power; uniform float u_bias; uniform float u_intensity;
// composite: uniform sampler2D u_ao_tex; uniform sampler2D u_input_tex; uniform sampler2D u_noise_tex;
// uniform float u_bilateral_radius; uniform float u_bilateral_sigma;
```


## Производительность

Cost scales with Samples (4–64) × (Resolution Scale)² (default 0.5 = quarter res) + bilateral blur radius (1–6).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/ssaop/component.py` — класс `SSAO`, `_inspector_fields`, дефолты `__init__`, `Depth-only AO: hemisphere kernel gather + bilateral blur composite. Kernel generated with fixed seed (NP_RNG = RandomState(42)).`.

- `core/components/rendering/postfx/ssaop/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
