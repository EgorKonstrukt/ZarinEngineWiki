# Blur

> Сепарабельное полноэкранное гауссово смягчение. Два прохода на итерацию (горизонталь, затем вертикаль) с гауссовыми весами от радиуса. Класс `Blur` в `core/components/rendering/postfx/blur.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Radius | `_radius` | FLOAT | 0.0 – 32.0 | `2.0` |
| Iterations | `_iterations` | INT | 1 – 8 | `1` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Blur`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.blur import Blur
fx = Blur()
entity.add_component(fx)
fx._radius = 4.0
fx._iterations = 2
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/blur.py (BLUR_FRAG)`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_radius;
uniform vec2 u_pixel_size;
// int r = int(min(u_radius, 64.0)); sigma = max(u_radius * 0.5, 0.5);
```


## Производительность

Cost scales with Radius (clamped to 64 in shader) × Iterations (1–8). Each iteration does a horizontal + vertical fullscreen pass via a temp FBO.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/blur.py` — класс `Blur`, `_inspector_fields`, дефолты `__init__`, `render_type = "screen"`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
