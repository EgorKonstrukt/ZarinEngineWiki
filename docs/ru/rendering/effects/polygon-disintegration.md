# Полигональный распад

> Разлёт треугольников. Поячеечный хэш (cell_size/noise_scale/stagger/thr_scale) гейтит выброс по amount; вылетевшие треугольники летят вдоль direction + внешняя нормаль со speed±variance, scatter/jitter, drag, gravity, spin/twist; fade гасит альфу; уцелевшая кайма светится edge_width/color/emission. Опционально animate/ping_pong ведёт amount. Класс `PolygonDisintegrationEffect` в `core/components/rendering/effects/polygon_disintegration_effect.py`. ObjectFx pass with GEOMETRY-shader path: fx_geometry_shader path ejects whole triangles (see spike_growth u_spike_* geometry precedent); double_sided controls culling in _render_object_effects.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.0` |
| Double Sided | `double_sided` | BOOL | — | `True` |
| Eject Direction | `direction` | VEC3 | — | `(0.0, 0.2, 0.0)` |
| Eject Speed | `speed` | FLOAT | — | `10.0` |
| Speed Variance | `sp_variance` | FLOAT | — | `4.0` |
| Outward Force | `outward` | FLOAT | — | `5.0` |
| Scatter | `scatter` | FLOAT | — | `3.0` |
| Jitter | `jitter` | FLOAT | — | `3.0` |
| Drag | `drag` | FLOAT | — | `12.0` |
| Gravity | `gravity` | FLOAT | — | `0.0` |
| Spin | `rotation` | FLOAT | — | `1.6` |
| Twist | `twist` | FLOAT | — | `1.4` |
| Fade Out | `fade` | SLIDER | 0.0 – 1.0 | `1.0` |
| Cell Size | `cell_size` | FLOAT | — | `0.08` |
| Shatter Noise | `noise_scale` | FLOAT | — | `4.6` |
| Cell Stagger | `stagger` | SLIDER | 0.0 – 1.0 | `0.595` |
| Threshold Scale | `thr_scale` | FLOAT | — | `1.45` |
| Dissolve Edge | `edge_width` | SLIDER | 0.0 – 0.5 | `0.5` |
| Edge Color | `edge_color` | COLOR | — | `[1.0, 0.55, 0.10]` |
| Edge Emission | `edge_emission` | FLOAT | — | `5.0` |
| Animate | `animate` | BOOL | — | `False` |
| Anim Speed | `speed_anim` | FLOAT | — | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `PolygonDisintegrationEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.polygon_disintegration_effect import PolygonDisintegrationEffect
fx = PolygonDisintegrationEffect()
entity.add_component(fx)
fx.amount = 0.4
fx.speed = 8.0
fx.animate = True
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_disint_* geometry + fragment branches)`. Привязанные uniform-ы: `u_disint_amount`, `u_disint_cell`, `u_disint_drag`, `u_disint_edge`, `u_disint_edge_emission`, `u_disint_fade`, `u_disint_gravity`, `u_disint_jitter`, `u_disint_noise_scale`, `u_disint_outward`, `u_disint_rot`, `u_disint_scatter`, `u_disint_sp_variance`, `u_disint_speed`, `u_disint_stagger`, `u_disint_thr_scale`, `u_disint_twist`, `u_double_sided`, `u_time`.

```glsl
// _apply uniforms (exact, 18): u_disint_amount, u_disint_cell, u_disint_speed, u_disint_sp_variance, u_disint_outward, u_disint_scatter, u_disint_jitter, u_disint_drag, u_disint_gravity, u_disint_rot, u_disint_twist, u_disint_fade, u_disint_noise_scale, u_disint_stagger, u_disint_thr_scale, u_disint_edge, u_disint_edge_emission (+ u_double_sided)
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/polygon_disintegration_effect.py` — класс `PolygonDisintegrationEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
