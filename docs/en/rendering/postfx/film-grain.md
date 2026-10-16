# Film Grain

> Animated procedural grain. Hash noise scaled by size, modulated by luminance influence, advanced by frame time. Class `FilmGrain` in `core/components/rendering/postfx/film_grain.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `0.1` |
| Size | `_size` | FLOAT | 0.1 – 10.0 | `1.0` |
| Luminance Influence | `_luminance_influence` | FLOAT | 0.0 – 1.0 | `0.5` |

## Usage

Editor: select the PostFX entity, Add Component > `FilmGrain`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.film_grain import FilmGrain
fx = FilmGrain()
entity.add_component(fx)
fx._intensity = 0.15
fx._size = 1.5
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/film_grain.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_size;
uniform float u_luminance_influence;
uniform float u_time;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/film_grain.py` — class `FilmGrain`, `_inspector_fields`, `__init__` defaults, `GraphicsEffect base, time-driven (u_time)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
