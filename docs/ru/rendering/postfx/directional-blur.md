# Направленное размытие

> Линейное мультитаповое размытие вдоль 2D-вектора направления. Число тапов и разброс — samples / strength. Класс `DirectionalBlur` в `core/components/rendering/postfx/directional_blur.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Direction | `_direction` | VEC2 | -1.0 – 1.0 | `(1.0, 0.0)` |
| Strength | `_strength` | SLIDER | 0.0 – 64.0 | `8.0` |
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `DirectionalBlur`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.directional_blur import DirectionalBlur
fx = DirectionalBlur()
entity.add_component(fx)
fx._strength = 12.0
fx._samples = 24
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/directional_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_direction;
uniform float u_strength;
uniform vec2 u_pixel_size;
uniform int u_samples;
```


## Производительность

Cost scales with Samples (4–64).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/directional_blur.py` — класс `DirectionalBlur`, `_inspector_fields`, дефолты `__init__`, `GraphicsEffect base, fullscreen pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
