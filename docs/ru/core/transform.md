# Трансформ

> `Transform` (`core.components.transform.tr`) хранит локальные position (`Vec3`), rotation (`Quat`), scale (`Vec3`) и лениво вычисляет мировую матрицу по цепочке родителей.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Дефолты: position `(0,0,0)`, rotation identity `Quat(0,0,0,1)`, scale `(1,1,1)`. Любой локальный сеттер помечает поддерево dirty (`_mark_dirty` идёт по детям, регистрирует корни в `Scene._dirty_roots`, флаги spatial + версии трансформа). `world_matrix` пересчитывается при чтении, если dirty: корневые сущности пишут локальную TRS-матрицу напрямую; дети умножают `local @ parent_world`. Инспектор редактирует `local_position` (Position), `local_euler_angles` в градусах (Rotation), `local_scale` (Scale). `Entity.set_parent(..., preserve_world=True)` сохраняет мировую матрицу через сеттер `world_matrix`. `position` читает мировую трансляцию; установка конвертируется в локалку через инверсную матрицу родителя. `forward` — `-Z` мировой матрицы (нормализованный), `right` — `+X`, `up` — `+Y`.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| local_position | Vec3 | `(0,0,0)` | Принимает `Vec3`, `ndarray`, tuple/list |
| local_rotation | Quat | identity | Сеттер нормализует, если дан `Quat` |
| local_scale | Vec3 | `(1,1,1)` | Принимает `Vec3`, `ndarray`, tuple/list |
| local_euler_angles | Vec3 | `(0,0,0)` deg | Эйлеров вид кватерниона (`Quat.from_euler` на set) |
| position | Vec3 | world origin | Трансляция в мировых координатах; сеттер конвертирует в локалку |
| world_matrix | Mat4 | identity | Ленивая; пересчёт пока `_dirty` |
| forward / right / up | Vec3 | axes | Нормализованные мировые оси (`-Z` / `+X` / `+Y`) |
| translate(delta, world_space=False) | method | — | Локальное сложение или через `position` в мире |
| rotate(euler) | method | — | `euler` в градусах; домножает локальный кватернион |
| look_at(target, up) | method | `up=None` (дефолт `Vec3.up()`) | Ставит `local_rotation` через `Quat.look_rotation` |
| world set | setter | — | Отложенно через `_world_target`, декомпозиция на flush |

## Использование в редакторе

Inspector → Transform: числовые поля Position / Rotation (градусы) / Scale. Drag гизмо вьюпорта пишет те же локалки. Репарент в Hierarchy сохраняет мировое положение (`preserve_world=True`).

## Использование в коде

```python
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3

tr = entity.add_component(Transform())
tr.local_position = Vec3(0.0, 1.0, 0.0)
tr.local_euler_angles = Vec3(0.0, 45.0, 0.0)
tr.local_scale = Vec3(2.0, 2.0, 2.0)

tr.translate(Vec3(0.0, 0.0, 1.0))   # local move
tr.rotate(Vec3(0.0, 90.0, 0.0))     # degrees
print(tr.position)                  # world-space Vec3
print(tr.forward)                   # world -Z
```

```python
tr.translate(Vec3(1.0, 0.0, 0.0), world_space=True)  # через мировую позицию
tr.look_at(Vec3(0.0, 1.0, 0.0))  # local_rotation через Quat.look_rotation
```

## Сериализация

```json
{ "type": "Transform", "enabled": true,
  "local_position": [0.0, 1.0, 0.0],
  "local_rotation": [0.0, 0.0, 0.0, 1.0],
  "local_scale": [1.0, 1.0, 1.0] }
```

Отсутствующие ключи откатываются на `[0,0,0]` / `[0,0,0,1]` / `[1,1,1]` в `Transform.deserialize`.

## Связанные страницы

- [Сущность Entity](./entity.md)
- [Обзор ECS](./ecs-overview.md)
- [Математика](../math/overview.md)

## Проверено по

- `core/components/transform/tr/__init__.py` (класс `Transform`, зарегистрирован)
- `core/components/transform/tr/component.py` (локалки, euler, `_inspector_fields`, дефолты)
- `core/components/transform/tr/matrix.py` (`world_matrix`, `position`, `forward/right/up`, `translate`, `rotate`, `look_at`)
- `core/components/transform/tr/hierarchy.py` (`_mark_dirty`, batch-апдейты)
- `core/components/transform/tr/serialize.py` (ключи + дефолты)
- `core/ecs/entity.py` (`set_parent` с сохранением мира)
