# Компоненты и жизненный цикл

> `Component` — данные плюс хуки жизненного цикла. Унаследуйтесь, повесьте `@ComponentRegistry.register`, реализуйте `serialize`/`deserialize`, и движок вызовет `on_awake → on_start → on_update(dt) / on_fixed_update(dt) → on_destroy`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`Component.__init_subclass__` автоопределяет покадровое участие: `_updates` равен `True`, когда подкласс переопределяет `on_update`, `_fixed_updates` — когда переопределяет `on_fixed_update`. Только компоненты активных сущностей с `enabled` сидят в диспетч-сетах сцены; сеттер `enabled` синхронизирует их немедленно. `on_awake` вызывается в `add_component` (даже в edit-режиме); `on_start` — один раз за `Scene.start()` (т.е. старт Play). `Scene.update` делит компоненты на constraints → остальные → IK; `Scene.fixed_update` идёт по кэшированному fixed-сету в порядке сета (порядок вставки не гарантируется). Исключения в колбэках ловятся покомпонентно и логируются как `Update error in <entity>/<component>` (аналогично для fixed). `ScriptComponent._allow_multiple = True`; большинство встроенных — одиночные.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| enabled | bool | `True` | Сеттер добавляет/убирает компонент из диспетч-сетов сцены |
| entity | Entity\|None | `None` | Владелец (ставит `add_component`) |
| transform | Component\|None | cached | `Transform` владеющей сущности, кэшируется на компоненте |
| _allow_multiple | bool | `False` | `True` → ключ `Class.<8-hex>`; напр. `ScriptComponent`, коллайдеры |
| _updates | bool | auto | `True`, если подкласс переопределяет `on_update` |
| _fixed_updates | bool | auto | `True`, если подкласс переопределяет `on_fixed_update` |
| on_awake | hook | `pass` | В момент `add_component` |
| on_start | hook | `pass` | Один раз за `Scene.start()` |
| on_update(dt) | hook | `pass` | Каждый кадр в Play-режиме |
| on_fixed_update(dt) | hook | `pass` | Каждый фиксированный шаг в Play-режиме |
| on_enable / on_disable | hook | `pass` | При активации / деактивации сущности |
| on_destroy | hook | `pass` | При удалении компонента/сущности/сцены |
| serialize | method | `{"type", "enabled"}` | Базовый dict; подклассы расширяют |
| gizmo hooks | methods | `gizmo_lines`, `gizmo_primitives`, `gizmo_instances`, … | Визуализация в редакторе; по умолчанию пусто |

## Использование в редакторе

Меню `Add Component` строится из `ComponentRegistry.all()` по вычисленным категориям. Поля инспектора — из `_inspector_fields()` (метаданные `InspectorField`). Мульти-инстанс компоненты показывают суффиксные ключи (`ScriptComponent.ab12cd34`) и управление порядком через `Entity.move_component(key, direction)`.

## Использование в коде

```python
from core.ecs import Component
from core.ecs import ComponentRegistry
from core.maths.math3d import Vec3

@ComponentRegistry.register
class Mover(Component):
    def __init__(self):
        super().__init__()
        self.speed = 5.0

    def on_update(self, dt: float):
        t = self.transform
        if t:
            t.translate(Vec3(0.0, 0.0, self.speed * dt))

    def serialize(self):
        d = super().serialize()
        d["speed"] = self.speed
        return d

    @classmethod
    def deserialize(cls, data):
        m = cls()
        m._enabled = bool(data.get("enabled", True))
        m.speed = float(data.get("speed", 5.0))
        return m
```

```python
from core.components.scripting.scriptc.component import ScriptComponent

comp.enabled = False  # вне диспетч-сетов сцены до повторного включения
multi = player.add_component(ScriptComponent())  # ключ вида "ScriptComponent.<8-hex>"
player.move_component(multi._key, -1)  # переупорядочить мульти-инстанс компоненты
```

## Сериализация

Базовый dict компонента: `{"type": "<ClassName>", "enabled": true, "_key": "<key>"}` (`_key` добавляет `Entity.serialize`). Подклассы дописывают свои поля; неизвестные имена `type` при загрузке пропускаются (`Entity.deserialize` проверяет `registry.get(ctype)`).

## Связанные страницы

- [Обзор ECS](./ecs-overview.md)
- [Сущность Entity](./entity.md)
- [Реестр и сериализация](./registry-serialization.md)
- [Скриптинг](../scripting/overview.md)
- [Примеры скриптов](../scripting/examples.md)

## Проверено по

- `core/ecs/component.py` (`__init_subclass__`, сеттер `enabled`, хуки, `serialize`/`deserialize`, кэш `transform`)
- `core/ecs/entity.py` (`add_component` → `on_awake`, `_make_component_key`, `move_component`)
- `core/ecs/query.py` (`run_update`, `run_fixed`, `run_start`, партиционирование)
- `core/ecs/registry.py` (`register`)
- `core/components/scripting/scriptc/component.py` (`_allow_multiple = True`)
