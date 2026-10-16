# SVGF

> Спатиотемпоральный variance-guided деноизер. Репроекция переиспользует историю через stability/disocclusion; проходы à-trous фильтруют по весам phi глубины/нормали/яркости; финал композитит. Класс `SpatiotemporalVarianceGuidedFilter` в `core/components/rendering/postfx/svgfp/component.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Temporal Stability | `_stability` | FLOAT | 0.0 – 0.99 | `0.9` |
| Disocclusion Depth | `_disocclusion` | FLOAT | 0.0 – 1.0 | `0.08` |
| Phi Depth | `_phi_depth` | FLOAT | 0.01 – 10.0 | `1.0` |
| Phi Normal | `_phi_normal` | FLOAT | 1.0 – 512.0 | `128.0` |
| Phi Luminance | `_phi_luma` | FLOAT | 0.1 – 16.0 | `4.0` |
| Iterations | `_iterations` | INT | 1 – 8 | `5` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `SpatiotemporalVarianceGuidedFilter`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.svgfp.component import SpatiotemporalVarianceGuidedFilter
fx = SpatiotemporalVarianceGuidedFilter()
entity.add_component(fx)
fx._stability = 0.92
fx._iterations = 5
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/svgfp/reproj.py (SVGF_REPROJ_FRAG), atrous.py (SVGF_ATROUS_FRAG), final.py (SVGF_FINAL_FRAG)`.

```glsl
// REPROJ: uniform sampler2D u_current; uniform sampler2D u_history; uniform sampler2D u_velocity; uniform float u_stability; uniform float u_disocclusion;
// ATROUS: uniform sampler2D u_color; uniform sampler2D u_depth; uniform sampler2D u_moment; uniform float u_phi_depth; uniform float u_phi_normal; uniform float u_phi_luma; uniform int u_step_size;
// FINAL: uniform sampler2D u_input;
```


## Производительность

Cost scales with Iterations (1–8, default 5) à-trous passes + history/moment buffers. History invalidated on resize (`_history_valid`).

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/svgfp/component.py` — класс `SpatiotemporalVarianceGuidedFilter`, `_inspector_fields`, дефолты `__init__`, `Temporal reprojection → à-trous wavelet (N iterations) → composite. Keeps history + moments + 2 atrous buffers with frame counter.`.

- `core/components/rendering/postfx/svgfp/reproj.py`
- `core/components/rendering/postfx/svgfp/atrous.py`
- `core/components/rendering/postfx/svgfp/final.py`
- `core/components/rendering/postfx/svgfp/vert.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
