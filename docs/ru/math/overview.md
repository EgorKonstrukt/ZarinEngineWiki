# Математика

`Vec2/Vec3/Vec4/Quat` (Cython `core/_math_vec`) плюс `Mat4` (NumPy `float64`) и кривые; flat-функции для горячих циклов.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `Vec2/Vec3/Vec4/Quat` | `core/_math_vec` через `core/maths/math3d.py` | `FLOAT_TYPE` float64, `FLT_EPSILON` |
| `Mat4.identity/translation/scale/perspective/orthographic/look_at` | `math3d.py` | Конструкторы; `__mul__/@`, `transposed`, `inverted`, `decompose()->(pos,Quat,scale)`, `batch_to_f32` |
| Flat-хелперы | `math3d.py` | `mat4_mul_flat`, `dot3_flat`, `cross3_flat`, `look_at_flat`, `mat4_invert_flat` |
| `convex_hull_simplices` | `convex_hull.py` | Cython или Python-фолбэк |
| `Curve / QuaternionCurve` | `foundation/curve.py` | Ключи для клипов и rolloff аудио |

## Использование в редакторе

1. Мат-типы — примитивы Inspector (`VEC3`, `FLOAT`, `CURVE`).
2. Кривые правятся в редакторе кривых (rolloff, ключи анимации).

## Использование в коде

```python
from core.maths.math3d import Vec3, Mat4

p = Vec3(1.0, 2.0, 3.0)
m = Mat4.translation(p) @ Mat4.scale(Vec3(2.0, 2.0, 2.0))
pos, rot, scl = m.decompose()
```

## Связанные

- Клип анимации, Constraints, NavAgent

## Проверено по

- `core/maths/math3d.py`, `core/maths/convex_hull.py`, `core/foundation/curve.py`
