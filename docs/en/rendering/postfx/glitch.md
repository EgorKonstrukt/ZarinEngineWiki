# Glitch

> Digital glitch: scanline block displacement + channel split, rate set by speed and band frequency, amount by intensity. Class `Glitch` in `core/components/rendering/postfx/glitch.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 3.0 | `0.5` |
| Speed | `_speed` | FLOAT | 0.0 – 20.0 | `3.0` |
| Frequency | `_frequency` | FLOAT | 1.0 – 100.0 | `20.0` |

## Usage

Editor: select the PostFX entity, Add Component > `Glitch`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.glitch import Glitch
fx = Glitch()
entity.add_component(fx)
fx._intensity = 1.0
fx._speed = 5.0
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/glitch.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_speed;
uniform float u_frequency;
uniform float u_time;
```


## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/glitch.py` — class `Glitch`, `_inspector_fields`, `__init__` defaults, `Time-driven fullscreen pass (u_time, u_resolution)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
