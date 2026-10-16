# Диссолв

> Выжигание по шумовому градиенту. Фрагменты за порогом amount (направленный градиент × noise_strength + шумовое поле × noise_scale) отбрасываются; полоса шириной edge_width светится edge_color × edge_emission. Опциональная анимация по времени ведёт amount 0→1 (ping-pong или цикл). Класс `DissolveEffect` в `core/components/rendering/effects/dissolve_effect.py`. ObjectFx pass (`core/renderer/object_fx_pass.py`): enabled DissolveEffect components are collected per entity (`_find_object_effect`) and bound in the FX program before the mesh draw.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `0.0` |
| Noise Strength | `noise_strength` | SLIDER | 0.0 – 1.0 | `1.0` |
| Noise Scale | `noise_scale` | FLOAT | 0.1 – 20.0 | `4.0` |
| Gradient Direction | `direction` | VEC3 | -1.0 – 1.0 | `(0.0, 1.0, 0.0)` |
| Invert | `invert` | BOOL | — | `False` |
| Edge Width | `edge_width` | SLIDER | 0.0 – 0.5 | `0.05` |
| Edge Color | `edge_color` | COLOR | — | `[1.0, 0.4, 0.05]` |
| Edge Emission | `edge_emission` | SLIDER | 0.0 – 5.0 | `2.5` |
| Animate | `animate` | BOOL | — | `False` |
| Speed | `speed` | FLOAT | 0.0 – 5.0 | `0.5` |
| Ping Pong | `ping_pong` | BOOL | — | `False` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `DissolveEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.dissolve_effect import DissolveEffect
fx = DissolveEffect()
entity.add_component(fx)
fx.amount = 0.5
fx.edge_width = 0.08
fx.animate = True
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_dissolve_* branch) + dissolve_effect.py::_apply`. Привязанные uniform-ы: `u_dissolve_amount`, `u_dissolve_edge`, `u_dissolve_color`, `u_dissolve_edge_emission`, `u_dissolve_noise_scale`, `u_dissolve_noise_strength`, `u_dissolve_dir`, `u_dissolve_invert`, `u_time`.

```glsl
// dissolve_effect.py::_apply (exact uniform names):
// prog["u_dissolve_amount"] = float(amount)
// prog["u_dissolve_edge"] = float(edge_width)
// prog["u_dissolve_color"] = vec3(edge_color)
// prog["u_dissolve_edge_emission"], prog["u_dissolve_noise_scale"], prog["u_dissolve_noise_strength"], prog["u_dissolve_dir"], prog["u_dissolve_invert"]
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/dissolve_effect.py` — класс `DissolveEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
