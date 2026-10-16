# Sharpen

> Unsharp-style detail lift. High frequency (center − neighbourhood mean) is added back scaled by intensity. Class `Sharpen` in `core/components/rendering/postfx/sharpen.py`. Runs in the PostFX stack in component order; skipped when `enabled` is False or the entity is inactive (`render()` early-out).

## Parameters

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |

## Usage

Editor: select the PostFX entity, Add Component > `Sharpen`, tick `enabled`, tune fields above. Order among PostFX components defines execution order.

Code:

```python
from core.components.rendering.postfx.sharpen import Sharpen
fx = Sharpen()
entity.add_component(fx)
fx._intensity = 0.8
fx.enabled = True
```

## Shader snippet

Shader: `core/components/rendering/postfx/sharpen.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform vec2 u_pixel_size;
```


## Performance notes

Fixed 3×3 neighbourhood, single pass.

## Related

Related: [PostFX Stack](../postfx.md), [Color Grading](color-grading.md), [Bloom](bloom.md).

## Verified against

- `core/components/rendering/postfx/sharpen.py` — class `Sharpen`, `_inspector_fields`, `__init__` defaults, `Single 3×3 contrast-adaptive pass (u_pixel_size)`.

- `core/components/rendering/postfx/graphics_effect.py` — `GraphicsEffect` base (`enabled`, `_registry`, `should_skip`, `render()` signature).
