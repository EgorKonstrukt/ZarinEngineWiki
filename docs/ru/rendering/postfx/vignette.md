# Виньетка

> Радиальное затемнение/тонирование краёв. Дистанция от центра формируется smoothness (спад) и roundness (компенсация аспекта), смешивается к цвету по intensity. Класс `Vignette` в `core/components/rendering/postfx/vignette.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.5` |
| Smoothness | `_smoothness` | FLOAT | 0.0 – 1.0 | `1.0` |
| Roundness | `_roundness` | FLOAT | 0.0 – 2.0 | `1.0` |
| Color | `_color` | COLOR | — | `(0.0, 0.0, 0.0)` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Vignette`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.vignette import Vignette
fx = Vignette()
entity.add_component(fx)
fx._intensity = 0.7
fx._smoothness = 0.8
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/vignette.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_intensity;
uniform float u_smoothness;
uniform float u_roundness;
uniform vec2 u_center;
// + uniform vec3 u_color;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/vignette.py` — класс `Vignette`, `_inspector_fields`, дефолты `__init__`, `Fullscreen radial mask pass (u_center is internal, fixed at screen center)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
