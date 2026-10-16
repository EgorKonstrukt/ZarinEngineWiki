# Ветер деревьев

> Ветер для деревьев. Нормированный wind_direction × wind_strength × wind_influence гнёт стволы/ветви на wind_speed; листва добавляет высокочастотный флаттер (leaf_flutter_speed/amount); turbulence_scale/amount — шум порывов. Быстрый путь выключения пишет _WindInfluence = 0. Класс `TreeWindEffect` в `core/components/rendering/effects/tree_wind_effect.py`. ObjectFx pass but with Tree material path: fx_geometry_shader() loads Tree.gshader (core/renderer/meshd.read_shader); _Wind* uniforms target the Tree/vegetation shader, not ObjectFx.shader.

## Параметры

| Inspector | Field | Type | Range | Default |
|---|---|---|---|---|
| Wind Influence | `wind_influence` | SLIDER | 0.0 – 1.0 | `1.0` |
| Wind Direction | `wind_direction` | VEC3 | -1.0 – 1.0 | `(1.0, 0.0, 0.5)` |
| Wind Speed | `wind_speed` | FLOAT | 0.0 – 20.0 | `2.0` |
| Wind Strength | `wind_strength` | FLOAT | 0.0 – 3.0 | `0.3` |
| Leaf Flutter Speed | `leaf_flutter_speed` | FLOAT | 0.0 – 30.0 | `8.0` |
| Leaf Flutter Amount | `leaf_flutter_amount` | FLOAT | 0.0 – 0.3 | `0.04` |
| Turbulence Scale | `turbulence_scale` | FLOAT | 0.0 – 5.0 | `0.5` |
| Turbulence Amount | `turbulence_amount` | SLIDER | 0.0 – 1.0 | `0.3` |

## Использование

Редактор: выбрать меш-сущность, Add Component > `TreeWindEffect`, включить `enabled`, анимировать amount-поля из скрипта.

Код:

```python
from core.components.rendering.effects.tree_wind_effect import TreeWindEffect
fx = TreeWindEffect()
entity.add_component(fx)
fx.wind_strength = 0.5
fx.wind_speed = 3.0
fx.enabled = True
# каждый кадр: fx.amount = min(1.0, fx.amount + dt * 0.5)
```

## Шейдер

Шейдер: `core/shaders/legacy/Tree.gshader via core/renderer/meshd.read_shader + core/shaders/materials/Tree.shader`. Привязанные uniform-ы: `_WindInfluence`, `_WindDir`, `_WindSpeed`, `_WindStrength`, `_LeafFlutterSpeed`, `_LeafFlutterAmount`, `_TurbulenceScale`, `_TurbulenceAmount`, `u_time`.

```glsl
// tree_wind_effect.py::_apply (exact names, note capital W):
// prog["_WindInfluence"], prog["_WindDir"], prog["_WindSpeed"], prog["_WindStrength"],
// prog["_LeafFlutterSpeed"], prog["_LeafFlutterAmount"], prog["_TurbulenceScale"], prog["_TurbulenceAmount"]
```

## Связанное

См. [Эффекты объектов](../effects.md), проход ObjectFx (`core/renderer/object_fx_pass.py`), `core/shaders/internal/ObjectFx.shader`.

## Проверено по коду

- `core/components/rendering/effects/tree_wind_effect.py` — класс `TreeWindEffect`, `_inspector_fields`, дефолты `__init__`, uniform-ы `_apply`.
- `core/components/rendering/effects/object_effect.py` — базовый `ObjectEffect` (`bind` ставит `u_time`, гварды `_set`/`_set_vec`, `reset_all_defaults`).
- `core/renderer/object_fx_pass.py` — `_find_object_effect`, `_render_object_effects`, `_get_fx_program`.
