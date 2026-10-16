# Квантование

> Постеризация. Каждый канал округляется до 2^bit_depth уровней, затем смешивается с исходником по intensity. Класс `Quantize` в `core/components/rendering/postfx/quantize.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Bit Depth | `_bit_depth` | FLOAT | 1.0 – 8.0 | `4.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Quantize`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.quantize import Quantize
fx = Quantize()
entity.add_component(fx)
fx._bit_depth = 3.0
fx._intensity = 1.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/quantize.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_bit_depth;
uniform float u_intensity;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/quantize.py` — класс `Quantize`, `_inspector_fields`, дефолты `__init__`, `Fullscreen color-level pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
