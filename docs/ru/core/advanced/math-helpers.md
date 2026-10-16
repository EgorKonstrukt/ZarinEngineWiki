# Math-хелперы

> `core/math_helpers.py` — числовой слой: `float64`-функции на сырых скалярах/`ndarray` для mat4, кватернионов, vec3 и рейкастов. Это чистый Python-фолбэк — собранный Cython `math_helpers.pyx` затеняет его при импорте ради скорости.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`FLOAT_TYPE = np.float64` по всему модулю. Все функции принимают и возвращают обычные float/кортежи/`ndarray` — без объектов `Vec`/`Mat4` — поэтому подходят для горячих циклов и батч-кода:

- mat4: `mat4_mul_fast(a, b)`, `mat4_inv_fast(m)` (адъюнкта; сингулярная `|det| < 1e-15` → identity), `mat4_translation`, `mat4_scale_mat`, `mat4_from_quaternion`, `mat4_to_f32_col_major` (16 float `float32` column-major для загрузки в GPU), `mat4_mul_vec3`, `mat4_look_at`, `mat4_perspective`, `mat3x3_inv`, `mat4_normal_matrix` (обратная-транспонированная как `float32` 3×3), `batch_matrices_to_f32` (`(n,4,4)` → `(n,16)` `float32`).
- Кватернионы: `quat_conjugate`, `quat_mul`, `quat_slerp` (со сменой знака и lerp-фолбэком при `dot > 0.9995`), `quat_normalize`, `quat_rotate_vec3`, `quat_from_euler` / `quat_to_euler` (градусы), `quat_from_euler_rad` / `quat_to_euler_rad`.
- vec3: `vec3_normalize` (ноль → `(0,0,0)`), `vec3_sub` / `add` / `scale` / `dot` / `cross`.
- Рейкасты (семейство Мёллера–Трумбора, `-1.0` при промахе): `ray_triangle_intersect` (эпсилон `1e-12`), `ray_mesh_intersect` (ближайшее попадание по тройкам индексов), `ray_sphere_intersect_batch` (struct-of-arrays батч), `ray_aabb_intersect` (slab-тест).

Используйте их вместо `Vec`/`Mat4` при работе с сырыми буферами, покадровыми батчами (циклы скининга/каллинга/рейкастов) или кодом солверов, где важен объектный оверхед. `Vec2/3/4`, `Quat`, `Mat4` — для состояния компонентов, значений инспектора и сериализации сцен. Понижение float64→float32 происходит только на границах загрузки (`to_f32`, `batch_to_f32`, `mat4_to_f32_col_major`, `mat4_normal_matrix`).

## Свойства

| группа функций | участники | примечания |
|---|---|---|
| построение mat4 | `mat4_translation`, `mat4_scale_mat`, `mat4_from_quaternion`, `mat4_look_at`, `mat4_perspective` | Row-major `float64`, трансляция в 3-й строке |
| алгебра mat4 | `mat4_mul_fast`, `mat4_inv_fast`, `mat3x3_inv`, `mat4_mul_vec3` | Развёрнутые циклы |
| кватернионы | `quat_mul`, `quat_slerp`, `quat_conjugate`, `quat_normalize`, `quat_rotate_vec3`, эйлеровы ×4 | Градусные и радианные варианты |
| vec3 | `vec3_normalize/sub/add/scale/dot/cross` | Скалярно-кортежный API |
| лучи | `ray_triangle_intersect`, `ray_mesh_intersect`, `ray_sphere_intersect_batch`, `ray_aabb_intersect` | `-1.0` при промахе |
| загрузка в GPU | `mat4_to_f32_col_major`, `mat4_normal_matrix`, `batch_matrices_to_f32` | Только здесь выход `float32` |

## Использование в редакторе

Прямой поверхности в редакторе нет; хелперы лежат под гизмо-математикой, нормалями импорта мешей, BVH/каллинг-батчами и трансформерами на стороне физики. Видимое в редакторе поведение (нормали, рейкаст-пикинг) идёт через них прозрачно.

## Использование в коде

```python
import numpy as np
from core import math_helpers as mh

m = mh.mat4_translation(1.0, 2.0, 3.0)
inv = mh.mat4_inv_fast(m)
print(mh.mat4_mul_vec3(m, 0.0, 0.0, 0.0))  # (1.0, 2.0, 3.0)

q = mh.quat_from_euler(0.0, 90.0, 0.0)
print(mh.quat_rotate_vec3(*q, 1.0, 0.0, 0.0))

t = mh.ray_triangle_intersect(0, 0, 5, 0, 0, -1,
                              -1, -1, 0, 1, -1, 0, 0, 1, 0)
print(t)  # 5.0, -1.0 при промахе

gpu = mh.batch_matrices_to_f32(np.stack([m, inv]))  # (2, 16) float32
```

## Сериализация

Здесь ничего не сериализуется; выходы — транзиентная числовка. Сохраняйте `Mat4`/`Quat` их собственными формами `to_list`/`to_dict`.

## Связанные страницы

- [Математика: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Точность и 64 бита](./precision-64bit.md)
- [Пространственные структуры](./spatial.md)
- [Многопоточность](./threading.md)

## Проверено по

- `core/math_helpers.py` (все имена функций, пороги, `FLOAT_TYPE`, комментарий про фолбэк)
- `core/pyx/math_helpers.pyx` (Cython-теневой модуль)
- `core/maths/math3d.py` (`to_f32`, `batch_to_f32`, `FLT_EPSILON`)
