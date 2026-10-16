# Панини

> Широкоугольная проекция Панини. Перемаппливает UV, уменьшая растяжение краёв на сверхшироких видах; distance — кривизна, crop_to_fit — баланс кропа и вписывания. Класс `PaniniProjection` в `core/components/rendering/postfx/panini.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Distance | `_distance` | FLOAT | 0.0 – 1.0 | `0.5` |
| Crop to Fit | `_crop_to_fit` | FLOAT | 0.0 – 1.0 | `1.0` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `PaniniProjection`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.panini import PaniniProjection
fx = PaniniProjection()
entity.add_component(fx)
fx._distance = 0.6
fx._crop_to_fit = 1.0
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/panini.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform vec2 u_view_extents;
uniform float u_distance;
uniform float u_crop_scale;
```


## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/panini.py` — класс `PaniniProjection`, `_inspector_fields`, дефолты `__init__`, `Fullscreen UV reprojection (u_view_extents)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
