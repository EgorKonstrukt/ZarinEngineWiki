# Справочник

Однострочный поиск по всем математическим функциям. `CY` = скомпилированный Cython (`core._math_vec` / `core.math_helpers` `.pyd` поверх `.py`), `FB` = чистый Python в `core/math_helpers.py` (шапка файла прямо называет его запасным вариантом).

## `_math_vec` (методы `Vec`/`Quat`)

| Имя | Сигнатура | Описание |
|---|---|---|
| `Vec2.__init__` | `(x=0.0, y=0.0)` | Конструктор, назначаемые `x`/`y` |
| `Vec2.from_array` | `(a)` | Из 2-последовательности |
| `Vec2.__add__/__sub__` | `(o)` | Покомпонентные +/− |
| `Vec2.__mul__/__rmul__/__truediv__` | `(s)` | Умножение/деление на скаляр |
| `Vec2.__neg__` | `()` | Отрицание |
| `Vec2.dot` | `(o)` | Скалярное произведение |
| `Vec2.length` | `()` | Длина |
| `Vec2.normalized` | `()` | Единичный; ноль при `l <= 1e-10` |
| `Vec2.to_list` | `()` | `[x, y]` |
| `Vec2.zero/one` | `()` | `(0,0)` / `(1,1)` |
| `Vec3.__init__` | `(x=0.0, y=0.0, z=0.0)` | Конструктор, назначаемый, индексный, итерируемый |
| `Vec3.from_array` | `(a)` | Из 3-последовательности |
| `Vec3.__add__/__sub__` | `(o)` | Покомпонентные +/− |
| `Vec3.__mul__/__rmul__/__truediv__` | `(s)` | Умножение/деление на скаляр |
| `Vec3.__neg__` | `()` | Отрицание |
| `Vec3.dot/cross` | `(o)` | Скалярное / векторное произведение |
| `Vec3.length/length_sq` | `()` | Длина / квадрат длины |
| `Vec3.distance_to/distance_sq_to` | `(o)` | Дистанция / квадрат дистанции |
| `Vec3.normalized` | `()` | Единичный; ноль при `l <= 1e-10` |
| `Vec3.lerp` | `(o, t)` | Линейная интерполяция, `t` не прижимается |
| `Vec3.to_array` | `()` | `float64`-массив |
| `Vec3.to_list` | `()` | `[x, y, z]` |
| `Vec3.zero/one/up/forward/right` | `()` | `(0,0,0)` / `(1,1,1)` / `(0,1,0)` / `(0,0,-1)` / `(1,0,0)` |
| `Vec4.__init__` | `(x=0.0, y=0.0, z=0.0, w=1.0)` | Конструктор, только чтение |
| `Vec4.from_array` | `(a)` | Из 4-последовательности |
| `Vec4.to_list` | `()` | `[x, y, z, w]` |
| `Quat.__init__` | `(x=0.0, y=0.0, z=0.0, w=1.0)` | Конструктор, только чтение |
| `Quat.from_array` | `(a)` | Из 4-последовательности |
| `Quat.identity` | `()` | `(0,0,0,1)` |
| `Quat.from_euler` | `(x, y, z)` | Градусы → кватернион |
| `Quat.from_axis_angle` | `(axis, angle_deg)` | Поворот вокруг нормализованной оси, градусы |
| `Quat.look_rotation` | `(forward, up=None→Vec3.up())` | Базис → кватернион |
| `Quat.__mul__` | `(o)` | Произведение Гамильтона (CY `quat_mul`) |
| `Quat.conjugate` | `()` | `(-x,-y,-z,w)` |
| `Quat.normalized` | `()` | Единичный (порог `1e-10`) |
| `Quat.rotate_vec3` | `(v)` | Повёрнутый `Vec3` |
| `Quat.to_euler` | `()` | `Vec3` в градусах |
| `Quat.to_matrix4` | `()` | Нормализация → `Mat4` |
| `Quat.slerp` | `(o, t)` | Slerp, lerp-запаска при `0.9995` |

## `math_helpers` (CY + FB, скалярный/`ndarray` API)

| Имя | Сигнатура | Описание |
|---|---|---|
| `mat4_mul_fast` | `(a, b)` | Умножение 4×4, развёрнутое |
| `mat4_inv_fast` | `(m)` | Обратная через адъюнкту; `|det|<1e-15` → единичная |
| `mat4_translation` | `(pos_x, pos_y, pos_z)` | Матрица трансляции, строка 3 |
| `mat4_scale_mat` | `(sx, sy, sz)` | Матрица масштаба |
| `mat4_from_quaternion` | `(x, y, z, w)` | Матрица поворота |
| `mat4_to_f32_col_major` | `(m)` | 16 `float32` column-major |
| `mat4_mul_vec3` | `(m, vx, vy, vz)` | Трансформ точки (w=1) |
| `mat4_mul_vec_flat` | `(m, x, y, z, w=1.0)` | То же для плоского списка 16 |
| `mat4_mul_flat` | `(l, r)` | Умножение плоских списков 16 |
| `mat4_look_at` | `(eye×3, center×3, up×3)` | Видовая матрица |
| `mat4_perspective` | `(fov_rad, aspect, near, far)` | Проекция, **радианы** |
| `mat3x3_inv` | `(m00…m22)` | Обратная 3×3; `|det|<1e-15` → единичная |
| `mat4_normal_matrix` | `(model)` | Обратная транспонированная `float32` 3×3 |
| `batch_matrices_to_f32` | `(matrices (n,4,4) f64)` | `(n,16)` `float32` |
| `quat_conjugate/mul/normalize` | `(x,y,z,w)[×2]` | Базовые операции |
| `quat_rotate_vec3` | `(qx…qw, vx, vy, vz)` | Поворот вектора |
| `quat_slerp` | `(a×4, b×4, t)` | Slerp, запаска при `0.9995` |
| `quat_from_euler/to_euler` | `(x,y,z)[,w]` | Градусные варианты |
| `quat_from_euler_rad/to_euler_rad` | `(x,y,z)[,w]` | Радианные варианты |
| `vec3_normalize/sub/add/scale/dot/cross` | скаляры | Кортежные vec3-операции |
| `dot3_flat/cross3_flat/normalize3_flat` | скаляры | Плоские тройки (порог нормы `1e-10`) |
| `rotate_around_axis_flat` | `(v×3, a×3, angle)` | Поворот Родригеса, **радианы** |
| `axis_vector_flat` | `(axis, sign=1.0)` | Базисный вектор-кортеж |
| `coordinate_system_axes` | `(cs)` | Перестановка осей |
| `is_right_handed_cs` | `(cs)` | `cs in (0,1,2)` |
| `look_at_flat` | `(eye, at, up, cs)` | Плоская view-матрица с учётом хиральности |
| `mat4_invert_flat` | `(m)` | Плоская обратная (без порога `1e-15`; сырой `1/det`) |
| `point_in_circle` | `(cx, cy, radius, px, py)` | 2D-принадлежность |
| `ray_triangle_intersect` | `(o×3, d×3, v0×3, v1×3, v2×3)` | `t` или `-1.0`, `1e-12` |
| `ray_mesh_intersect` | `(o×3, d×3, verts, indices)` | Ближайшее `t` или `-1.0` |
| `ray_aabb_intersect` | `(o×3, d×3, bmin×3, bmax×3)` | Slab-`t` или `-1.0` |
| `ray_sphere_intersect_batch` | `(SoA-массивы…, results)` | Пакетные сферы `t`/`-1.0` |

## `math3d` дополнительно (слой NumPy/`Mat4`)

| Имя | Сигнатура | Описание |
|---|---|---|
| Конструкторы `Mat4` | `identity/translation/scale/perspective(fov_deg)/orthographic/look_at` | `float64`-билдеры |
| Алгебра `Mat4` | `__mul__/__matmul__/transposed/inverted` | `inverted`: `LinAlgError` → единичная |
| Экспорт `Mat4` | `to_array/to_f32/batch_to_f32/to_list/get_translation/decompose` | Граница CPU→GPU |
| `FLT_EPSILON` | `1.1920928955078125e-07` | Эпсилон float32 (в `math3d`, не в `constants.py`) |
| `FLOAT_TYPE` | `np.float64` | Канонический тип |

## Связанные страницы

- [Обзор математики](overview.md)
- [Векторы](vectors.md), [Матрицы](matrices.md), [Кватернионы](quaternions.md), [Рейкастинг](raycast.md)
- [Math Helpers](../core/advanced/math-helpers.md), [Точность и 64 бит](../core/advanced/precision-64bit.md)
- [Curve API](../core/advanced/curve-api.md)

## Проверено по

- `core/pyx/_math_vec.pyx:1-470` (все методы; отсутствие `Vec3.rotate`, `Vec2.lerp`, операций `Vec4`, `Quat.lerp`)
- `core/math_helpers.py:1-381` (полный список функций, шапка fallback)
- `core/pyx/math_helpers.pyx:1-571` (Cython-зеркало)
- `core/maths/math3d.py:15-16`, `:108`, `:134-311` (плоские хелперы)
