# Божественные лучи

> Реймарш световых столбов. Яркий проход выше порога рассеивается к экранной позиции света за N сэмплов, масштаб — exposure × intensity. Класс `GodRays` в `core/components/rendering/postfx/godrays/component.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Intensity | `_intensity` | FLOAT | 0.0 – 5.0 | `0.8` |
| Exposure | `_exposure` | FLOAT | 0.0 – 0.5 | `0.01` |
| Threshold | `_threshold` | SLIDER | 0.05 – 0.5 | `0.25` |
| Samples | `_num_samples` | INT | 8 – 256 | `64` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `GodRays`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.godrays.component import GodRays
fx = GodRays()
entity.add_component(fx)
fx._intensity = 1.2
fx._num_samples = 96
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/godrays/shaders.py`.

```glsl
// GOD_RAYS frag uniforms (exact names):
// uniform sampler2D u_scene_color; uniform sampler2D u_depth_tex;
// uniform vec2 u_light_uv; uniform vec3 u_light_color; uniform float u_light_intensity;
// uniform float u_threshold; uniform float u_intensity; uniform float u_exposure; uniform int u_num_samples;
```


## Производительность

Cost scales linearly with Samples (8–256, default 64). Uses scratch color/depth targets (`_scratch_color`, `_scratch_depth`).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/godrays/component.py` — класс `GodRays`, `_inspector_fields`, дефолты `__init__`, `Radial raymarch from light UV; occluded by depth (u_depth_tex)`.

- `core/components/rendering/postfx/godrays/shaders.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
