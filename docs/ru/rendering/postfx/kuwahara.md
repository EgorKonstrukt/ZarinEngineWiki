# Кувахара

> Фильтр масляной живописи. Дисперсия по квадрантам выбирает среднее самого плоского региона; intensity подмешивает обратно к фото. Класс `Kuwahara` в `core/components/rendering/postfx/kuwahara.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Radius | `_radius` | FLOAT | 1.0 – 4.0 | `2.0` |
| Intensity | `_intensity` | FLOAT | 0.0 – 1.0 | `1.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `Kuwahara`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.kuwahara import Kuwahara
fx = Kuwahara()
entity.add_component(fx)
fx._radius = 3.0
fx._intensity = 0.8
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/kuwahara.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform float u_radius;
uniform float u_intensity;
uniform vec2 u_pixel_size;
```


## Производительность

Tap count grows with Radius² (1–4).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/kuwahara.py` — класс `Kuwahara`, `_inspector_fields`, дефолты `__init__`, `Fullscreen anisotropic smoothing pass`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
