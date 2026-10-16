# FSR2

> Темпоральный апскейлер. EASU апскейлит, темпоральный проход репроецирует по velocity/depth с variance-клипом, RCAS шарпит. Stability/disocclusion управляют переиспользованием истории. Класс `FidelityFXSuperResolution2` в `core/components/rendering/postfx/fsr2p/effect.py`. Выполняется в стеке PostFX в порядке компонентов; пропускается при `enabled == False` или неактивной сущности (ранний выход в `render()`).

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Temporal Stability | `_stability` | FLOAT | 0.0 – 0.99 | `0.9` |
| RCAS Attenuation | `_rcas_sharpness` | FLOAT | 0.0 – 1.0 | `0.2` |
| Disocclusion Depth | `_disocclusion` | FLOAT | 0.0 – 1.0 | `0.15` |
| Variance Clip Gamma | `_variance_gamma` | FLOAT | 0.5 – 4.0 | `1.25` |
| EASU Edge Sharpness | `_easu_sharpness` | FLOAT | 0.0 – 1.0 | `0.35` |

## Использование

Редактор: выбрать PostFX-сущность, Add Component > `FidelityFXSuperResolution2`, включить `enabled`, настроить поля выше. Порядок среди PostFX-компонентов задаёт порядок выполнения.

Код:

```python
from core.components.rendering.postfx.fsr2p.effect import FidelityFXSuperResolution2
fx = FidelityFXSuperResolution2()
entity.add_component(fx)
fx._stability = 0.9
fx._rcas_sharpness = 0.2
fx.enabled = True
```

## Шейдер

Шейдер: `core/components/rendering/postfx/fsr2p/easu.py (EASU_FRAG), temporal.py (TEMPORAL_FRAG), rcas.py (RCAS_FRAG)`.

```glsl
// EASU: uniform sampler2D u_src; uniform vec2 u_src_size; uniform vec2 u_src_pixel; uniform float u_sharpness;
// TEMPORAL: uniform sampler2D u_current; uniform sampler2D u_history; uniform sampler2D u_velocity; uniform float u_stability; uniform float u_variance_gamma;
// RCAS: uniform sampler2D u_rcas_input; uniform vec2 u_rcas_size; uniform vec2 u_rcas_con;
```


## Производительность

Three fullscreen passes + 2-frame history buffers. Internal `_history_valid` / `_cur_index` ping-pong; history invalidated on resize.

## Связанное

См. [Стек PostFX](../postfx.md), [Цветокоррекция](color-grading.md), [Bloom](bloom.md).

## Проверено по коду

- `core/components/rendering/postfx/fsr2p/effect.py` — класс `FidelityFXSuperResolution2`, `_inspector_fields`, дефолты `__init__`, `Multi-pass chain: EASU (edge-adaptive upscale) → TEMPORAL (reprojection + variance clip) → RCAS (sharpen). Keeps 2-frame history (u_history) + variance gamma clip.`.

- `core/components/rendering/postfx/fsr2p/easu.py`
- `core/components/rendering/postfx/fsr2p/rcas.py`
- `core/components/rendering/postfx/fsr2p/temporal.py`
- `core/components/rendering/postfx/fsr2p/vert.py`
- `core/components/rendering/postfx/graphics_effect.py` — базовый `GraphicsEffect` (`enabled`, `_registry`, `should_skip`, сигнатура `render()`).
