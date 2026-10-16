# Curve API

> `Curve` — кейфреймовая float-функция: `CurveKey(time, value, in/out_tangent, tangent_mode)` с `TangentMode` `FREE` / `LINEAR` / `CONSTANT` / `SMOOTH`. `evaluate(t)` клампит вне диапазона ключей и Эрмит-интерполирует внутри; `to_list` хранит `[[time, value]]`.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

`Curve` (`@dataclass`, поля `keys`, `pre_wrap="clamp"`, `post_wrap="clamp"`) и `CurveKey` (`time`, `value`, `in_tangent=0.0`, `out_tangent=0.0`, `tangent_mode=SMOOTH`) живут в `core/foundation/curve.py`.

- `add_key(time, value)` добавляет и пересортировывает по времени, затем `_auto_smooth()` пересчитывает касательные только `SMOOTH`-ключей: концы — по склону к соседу, внутренние — по хорде через обоих соседей. Возвращает новый `CurveKey`. `remove_key(key)` и `find_key(time)` (эпсилон `1e-6`) управляют ключами.
- `evaluate(time)`: пустая кривая → `0.0`; один ключ → его значение; вне `[first, last]` → кламп к концу. Внутри сегмента: режим `CONSTANT` на левом ключе возвращает левое значение; `LINEAR` — лерп; иначе кубический Эрмит с `m0 = out_tangent * dt`, `m1 = in_tangent * dt`.
- `evaluate_array(times)` использует Cython-путь `evaluate_curve_batch`, если импортируется (режимы отображаются `FREE/SMOOTH→0`, `LINEAR→1`, `CONSTANT→2`), иначе — поэлементный `evaluate`.
- Персистентность: `to_list()` → `[[time, value]]`; `from_list(data)` пересоздаёт (берутся записи с ≥ 2 элементами); `to_dict()` / `from_dict()` хранят касательные, строки `tangent_mode` и wrap-режимы; `copy()` — круговой проход через dict.
- `QuaternionCurve` — вариант для вращений: ключи держат 4-кортежи (или `Quat`), `evaluate` делает slerp между соседями, пустая → `(0.0, 0.0, 0.0, 1.0)`; `keys_dict()` / `from_keys_dict()` сохраняют как `[{"time", "value": [...]}]`.

## Свойства

| имя | тип | дефолт | описание |
|---|---|---|---|
| `TangentMode` | Enum | `SMOOTH` | `free` / `linear` / `constant` / `smooth` |
| `CurveKey` | dataclass | — | `time`, `value`, `in/out_tangent`, `tangent_mode` |
| `Curve` | dataclass | `clamp`/`clamp` | `keys`, `pre_wrap`, `post_wrap` |
| `add_key(time, value)` | метод | — | Вставка с сортировкой + пересглаживание SMOOTH |
| `evaluate(t)` | метод | `0.0` на пустой | Клампнутый Эрмит / LINEAR / CONSTANT |
| `evaluate_array(times)` | метод | — | Numpy-батч, Cython fast path с фолбэком |
| `to_list()` / `from_list()` | методы | — | Компактная форма `[[t, v]]` |
| `to_dict()` / `from_dict()` / `copy()` | методы | — | Полная форма с касательными и wrap |
| `QuaternionCurve` | класс | identity на пустой | Slerp-интерполяция ключей вращения |

## Использование в редакторе

Поля `CURVE` в инспекторе (например PhysBone `pull_curve`, `spring_curve`, `gravity_curve`) открывают панель редактора кривых, которая правит тот же объект `Curve` (ключи, касательные, `TangentMode` на ключ).

## Использование в коде

```python
from core.foundation.curve import Curve, TangentMode, QuaternionCurve

curve = Curve()
k0 = curve.add_key(0.0, 0.0)
k1 = curve.add_key(1.0, 10.0)
k0.tangent_mode = TangentMode.LINEAR
print(curve.evaluate(0.5))   # 5.0
print(curve.to_list())       # [[0.0, 0.0], [1.0, 10.0]]

import numpy as np
print(curve.evaluate_array(np.array([0.0, 0.25, 0.5, 1.0])))

rot = QuaternionCurve()
rot.add_key(0.0, (0.0, 0.0, 0.0, 1.0))
print(rot.evaluate(0.0))
```

## Сериализация

`to_list` — компактная форма для сцен; `to_dict` — полная (ключи с `time/value/in_tangent/out_tangent/tangent_mode`, `pre_wrap`, `post_wrap`). `ScriptComponent` хранит кривые в своём dict скриптовых полей.

## Связанные страницы

- [Foundation: Curve, команды, Time Travel](../foundation.md)
- [Каталог полей инспектора](./inspector-fields-catalog.md)
- [Математика: Vec, Quat, Mat4, Curve](../../math/overview.md)
- [Редакторы кривых, градиентов, цвета](../../editor/curve-gradient-editors.md)

## Проверено по

- `core/foundation/curve.py` (`TangentMode`, `CurveKey`, `Curve`, `QuaternionCurve`, `_quat_slerp`, маппинг batch-режимов)
- `core/components/physics/physbone/params.py` (использование `FieldType.CURVE`)
- `core/components/scripting/scriptc/fields.py` (аннотация `Curve` → `FieldType.CURVE`)
