# Дисторсия линзы

> Бочка/подушка с поканальным масштабированием (хроматическая кайма) и компенсацией масштаба центра. Класс `LensDistortion` в `core/components/rendering/postfx/lens_distortion.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Strength | `_strength` | FLOAT | -1.0 – 1.0 | `0.3` |
| Chromatic | `_chromatic` | FLOAT | 0.0 – 1.0 | `0.5` |
| Scale | `_scale` | FLOAT | 0.5 – 1.5 | `0.9` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `LensDistortion`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.lens_distortion import LensDistortion
fx = LensDistortion()
entity.add_component(fx)
fx._strength = 0.35
fx._chromatic = 0.4
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/lens_distortion.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_strength;
uniform float u_chromatic;
uniform float u_scale;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/lens_distortion.py` — класс `LensDistortion`, `_inspector_fields`, дефолты `__init__`, `Fullscreen UV warp pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
