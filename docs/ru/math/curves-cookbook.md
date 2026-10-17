# Кривые: рецепты

Эталонное поведение — в [Curve API](../core/advanced/curve-api.md); здесь проверенные шаблоны использования. `Curve`/`QuaternionCurve` определены в `core/foundation/curve.py`.

## Факты, используемые ниже

- `TangentMode`: `FREE` / `LINEAR` / `CONSTANT` / `SMOOTH` (дефолт ключа: `SMOOTH`).
- `evaluate(t)`: пустая → `0.0`; один ключ → его значение; вне диапазона → прижим к концам; `CONSTANT` слева → левое значение; `LINEAR` → lerp; иначе кубический Эрмит (пороги `1e-10`). Нюанс: скалярный `evaluate` всегда прижимает — `pre_wrap`/`post_wrap` (дефолт `"clamp"`) передаются только в Cython-путь `evaluate_array`.
- `evaluate_array(times)`: Cython `evaluate_curve_batch`, если импортируется (маппинг `FREE`/`SMOOTH`→0, `LINEAR`→1, `CONSTANT`→2), иначе поточечный `evaluate`.
- `to_list()` → `[[time, value]]`; `from_list` пересобирает (записи с ≥ 2 элементами).
- `QuaternionCurve`: пустая → `(0,0,0,1)`; slerp между соседями с lerp-запаской при `dot > 0.9995` и порогом нормализации `1e-12`; `add_key` сортирует по времени.

## Покачивание (зацикливание вручную)

```python
import math
from core.foundation.curve import Curve

bob = Curve.from_list([[0.0, 0.0], [0.5, 1.0], [1.0, 0.0]])
t = 0.0
def on_update(dt):
    global t
    t = (t + dt * 0.5) % 1.0          # цикл вручную: wrap-режимы только clamp
    entity.transform.translate(__import__("core.maths.math3d", fromlist=["Vec3"]).Vec3(0, bob.evaluate(t) * 0.3 * dt * 60 * 0.016, 0))
```

Более простой вариант с абсолютным смещением:

```python
y = bob.evaluate(t) * 0.5            # смещение 0..0.5 м
p = entity.transform.position
entity.transform.position = Vec3(p.x, base_y + y, p.z)
```

## Дверь (LINEAR против SMOOTH)

```python
from core.foundation.curve import Curve, TangentMode

ease = Curve()
k0 = ease.add_key(0.0, 0.0)
k1 = ease.add_key(1.0, 90.0)
k0.tangent_mode = TangentMode.SMOOTH   # плавный взмах Эрмита
# k0.tangent_mode = TangentMode.LINEAR # равномерный взмах
# k0.tangent_mode = TangentMode.CONSTANT # дверь закрыта до t >= 1.0

def on_update(dt):
    global t
    t = min(t + dt / 2.0, 1.0)         # открытие за 2 секунды
    hinge.rotate(Vec3(0.0, ease.evaluate(t) - hinge.local_euler_angles.y, 0.0))
```

## Анимация света (пакетная выборка)

```python
import numpy as np
from core.foundation.curve import Curve

flicker = Curve.from_list([[0.0, 1.0], [0.1, 0.6], [0.2, 1.0], [1.0, 0.9]])
samples = flicker.evaluate_array(np.linspace(0.0, 1.0, 60))  # Cython-пакет, если доступен
print(flicker.to_list())  # [[0.0, 1.0], [0.1, 0.6], [0.2, 1.0], [1.0, 0.9]]
```

## Ключи поворота

```python
from core.foundation.curve import QuaternionCurve
from core.maths.math3d import Quat

spin = QuaternionCurve()
spin.add_key(0.0, (0.0, 0.0, 0.0, 1.0))
spin.add_key(1.0, Quat.from_euler(0.0, 180.0, 0.0))  # Quat принимается
print(spin.evaluate(0.5))   # slerp-середина (кортеж)
print(spin.keys_dict())     # [{"time":..., "value":[...]}]
```

## Связанные страницы

- [Curve API](../core/advanced/curve-api.md) (эталон)
- [Обзор математики](overview.md), [Интерполяция](interpolation.md)
- [Input и Math API](../scripting/input-math-api.md)

## Проверено по

- `core/foundation/curve.py:14-27` (`TangentMode`, `CurveKey`), `:31-34` (дефолты), `:36-70` (`add_key`, `_auto_smooth`), `:72-98` (`evaluate`), `:100-130` (`evaluate_array`, маппинг), `:132-141` (`to_list`/`from_list`), `:239-269` (`QuaternionCurve`), `:191-221` (пороги slerp/нормализации)
