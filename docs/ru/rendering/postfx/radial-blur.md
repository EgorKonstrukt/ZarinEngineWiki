# Радиальный блюр

> Зум/спин-размытие вокруг экранного центра. Сэмплы собираются вдоль векторов центр→пиксель; пиксели с движением ниже порога остаются резкими. Класс `RadialBlur` в `core/components/rendering/postfx/radial_blur.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Center | `_center` | VEC2 | 0.0 – 1.0 | `(0.5, 0.5)` |
| Strength | `_strength` | SLIDER | 0.0 – 0.5 | `0.05` |
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |
| Threshold | `_threshold` | SLIDER | 0.0 – 0.01 | `0.001` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `RadialBlur`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.radial_blur import RadialBlur
fx = RadialBlur()
entity.add_component(fx)
fx._strength = 0.08
fx._samples = 24
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/radial_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_velocity_tex;
uniform vec2 u_center;
uniform float u_strength;
uniform float u_threshold;
```


## Производительность

Cost scales with Samples (4–64).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/radial_blur.py` — класс `RadialBlur`, `_inspector_fields`, дефолты `__init__`, `Center-anchored gather; optional velocity path (u_velocity_tex / u_use_velocity)`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
