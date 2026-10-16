# Качание ветра

> Общее качание вершин. Нормированный direction × strength × amount смещает вершины с фазой от u_time × speed; быстрый путь пишет u_wind_amount = 0 при выключении или amount ≤ 0. Класс `WindSwayEffect` в `core/components/rendering/effects/wind_sway_effect.py`. ObjectFx pass vertex bend (generic props/grass), same collection/bind path as Dissolve.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Amount | `amount` | SLIDER | 0.0 – 1.0 | `1.0` |
| Wind Direction | `direction` | VEC3 | -1.0 – 1.0 | `(1.0, 0.0, 0.0)` |
| Speed | `speed` | FLOAT | 0.0 – 20.0 | `2.0` |
| Sway | `strength` | FLOAT | 0.0 – 3.0 | `0.3` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `WindSwayEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.wind_sway_effect import WindSwayEffect
fx = WindSwayEffect()
entity.add_component(fx)
fx.amount = 1.0
fx.strength = 0.5
fx.speed = 3.0
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/internal/ObjectFx.shader (u_wind_* branch)`. Привязанные uniform-ы: `u_wind_amount`, `u_wind_dir`, `u_wind_speed`, `u_wind_strength`, `u_time`.

```glsl
// wind_sway_effect.py::_apply (exact):
// prog["u_wind_amount"] = float(amount)  // 0 fast-path when disabled
// prog["u_wind_dir"] = normalized vec3, prog["u_wind_speed"], prog["u_wind_strength"]
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/wind_sway_effect.py` — класс `WindSwayEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
