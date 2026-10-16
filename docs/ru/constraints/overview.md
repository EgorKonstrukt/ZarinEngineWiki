# Ограничения Constraints

Покадровые followers Трансформа: бленд позиции/вращения/масштаба, look-at/aim, родительство и движение. Все — компоненты реестра с `is_active`.

## Свойства / API

| Компонент | Поля | Поведение |
|---|---|---|
| `PositionConstraint` | `sources[{entity_id, weight}]`, `weight_x/y/z` | Средневзвешенная позиция + offset |
| `RotationConstraint` | источники + веса осей | Бленд вращения |
| `ScaleConstraint` | источники + веса осей | Бленд масштаба |
| `ParentConstraint` | источник | Копия мирового трансформа |
| `FollowTransformConstraint` | цель + офсеты | Жёсткое следование |
| `LookAtConstraint` | цель, up | Разворот forward к цели |
| `AimConstraint` | цель, ось | Наведение локальной оси |
| `MoveTowardsConstraint` | цель, скорость | Шаг к цели |
| `RotateTowardsConstraint` | цель, угловая скорость | Slerp к цели |
| `ScaleToConstraint` | целевой масштаб | Lerp масштаба |

Общее: `add_source(entity, weight)`, `remove_source(index)`, `set_offset_from_current_position()`, `serialize()` / `deserialize()`.

## Использование в редакторе

1. Добавьте constraint на follower-сущность.
2. Назначьте источники с весами; `set_offset_from_current_position` сохраняет текущий зазор.
3. Весами осей лочите оси; `is_active` выключает.

## Использование в коде

```python
c = entity.get_component_by_name("PositionConstraint")
c.add_source(leader_entity, weight=1.0)
c.set_offset_from_current_position()
c.weight_y = 0.0
```

## Связанные

- Навигация, анимация (пути костей), Transform

## Проверено по

- `core/components/constraints/__init__.py`, `position_constraint.py` и соседние файлы
