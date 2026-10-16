# Собель

> Яркостный Собель с 5 режимами композитинга (int 0–4): 0 сцена→цвет края, 1 белый→цвет края, 2 сцена→чёрный, 3 цвет направления градиента, 4+ аддитивное свечение. Thickness масштабирует оффсеты 3×3; порог — smoothstep(threshold, threshold+0.5). Класс `Sobel` в `core/components/rendering/postfx/sobel.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Mode | `_mode` | INT | 0 – 4 | `0` |
| Threshold | `_threshold` | FLOAT | 0.0 – 2.0 | `0.1` |
| Thickness | `_thickness` | FLOAT | 0.5 – 8.0 | `1.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 3.0 | `1.0` |
| Edge Color | `_edge_color` | COLOR | — | `(0.0, 0.0, 0.0)` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Sobel`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.sobel import Sobel
fx = Sobel()
entity.add_component(fx)
fx._mode = 0
fx._thickness = 1.5
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/sobel.py (SOBEL_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_pixel_size;
uniform float u_threshold;
uniform float u_thickness;
uniform float u_intensity;
uniform vec3 u_edge_color;
uniform int u_mode;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/sobel.py` — класс `Sobel`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen", _intensity_prop = "_intensity"`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
