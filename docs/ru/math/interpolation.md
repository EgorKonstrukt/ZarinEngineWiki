# Интерполяция

Здесь только хелперы, существующие в коде.

## Что есть

| Хелпер | Расположение | Примечания |
|---|---|---|
| `Vec3.lerp(o, t)` | `_math_vec.pyx:232-237` | `a + (b-a)*t`; `t` не прижимается |
| `Quat.slerp(o, t)` | `_math_vec.pyx:458-464` + `math_helpers.quat_slerp` | отражение при `dot < 0`; lerp-запаска при `dot > 0.9995`; перенормировка (порог `1e-10`) |
| `math_helpers.quat_slerp(...)` | `math_helpers.py:97-119` | скалярно-кортежный вариант |
| `Curve.evaluate(t)` | `curve.py:72-98` | Эрмит / `LINEAR`-lerp / `CONSTANT`-ступенька |
| `QuaternionCurve.evaluate(t)` | `curve.py:254-269` | slerp между соседними ключами |

## Чего НЕТ

Хелперов `damp`, `smooth_damp`, `exp_damp`, `move_towards`, `lerp_angle`, `smoothstep` в математическом коде не найдено. Найденные `damping` — это параметры физики/частиц (`soft_body.damping`, `damping_curve` у PhysBone, `sim_damping` воды, поле `damping` editor-камеры), а не переиспользуемые функции; `smoothstep` — лишь локальный шейдерный хелпер в `hatching.py:259`. Кадрово-независимое сглаживание делайте сами масштабированием фактора на `dt` (ниже).

## Кадровая независимость через `dt`

`lerp`/`slerp` принимают сырой `t` без понятия времени. Ведите `t` от накопленного времени либо используйте экспоненциальное взвешивание:

```python
from core.maths.math3d import Vec3, Quat

# Движение по времени (детерминировано, масштабировано dt):
pos = Vec3(0, 0, 0)
target = Vec3(10, 0, 0)
progress = 0.0
def on_update(dt):
    global progress, pos
    progress = min(progress + dt / 2.0, 1.0)  # путь за 2 с
    pos = Vec3(0, 0, 0).lerp(target, progress)

# Покадровое экспоненциальное приближение (кадрово-независимый фактор):
smooth = 5.0
current = Vec3(0, 0, 0)
def on_update_exp(dt):
    global current
    import math
    k = 1.0 - math.exp(-smooth * dt)   # собственное взвешивание, не код движка
    current = current.lerp(target, k)

# Поворот:
a = Quat.identity()
b = Quat.from_euler(0.0, 90.0, 0.0)
mid = a.slerp(b, 0.5)
```

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Кватернионы](quaternions.md), [Кривые: рецепты](curves-cookbook.md)
- [Curve API](../core/advanced/curve-api.md)
- [Input и Math API](../scripting/input-math-api.md) (паттерн `dt` / `Input.deltaTime`)

## Проверено по

- `core/pyx/_math_vec.pyx:232-237` (`Vec3.lerp`), `:458-464` (`Quat.slerp`)
- `core/math_helpers.py:97-119` (`quat_slerp`)
- `core/foundation/curve.py:72-98`, `:254-269`
- Отсутствие проверено поиском `damp*`, `move_towards`, `smoothstep` по математическим путям (только нематематические совпадения выше)
