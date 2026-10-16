# Моушн-блюр

> Накопление по движению камеры/объектов. Каждый пиксель репроецируется (velocity-буфер или prev/inv view-proj + depth) и накапливается за N сэмплов вдоль вектора движения; пиксели ниже порога остаются резкими. Класс `MotionBlur` в `core/components/rendering/postfx/motion_blur.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Samples | `_samples` | INT_SLIDER | 4 – 64 | `16` |
| Strength | `_strength` | SLIDER | 0.0 – 1.5 | `1.0` |
| Threshold | `_threshold` | SLIDER | 0.0 – 0.01 | `0.001` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `MotionBlur`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.motion_blur import MotionBlur
fx = MotionBlur()
entity.add_component(fx)
fx._samples = 24
fx._strength = 0.8
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/motion_blur.py`.

```glsl
#version 330 core
uniform sampler2D u_input_tex;
uniform sampler2D u_velocity_tex;
uniform sampler2D u_depth_tex;
uniform mat4 u_inv_view_proj;
uniform mat4 u_prev_view_proj;
// + u_samples, u_strength, u_threshold, u_use_velocity, u_use_velocity_tex
```


## Производительность

Cost scales with Samples (4–64). Requires velocity or valid prev-frame matrices; falls back to depth reprojection.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/motion_blur.py` — класс `MotionBlur`, `_inspector_fields`, дефолты `__init__`, `Velocity / view-projection reprojection gather. Uses u_velocity_tex when present, else reconstructs from u_prev_view_proj × u_inv_view_proj + depth.`.

- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
