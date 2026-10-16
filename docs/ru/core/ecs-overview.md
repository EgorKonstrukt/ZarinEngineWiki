# Обзор ECS

> ZarinEngine — движок с Entity-Component-System архитектурой: объекты `Entity` хранят данные, `Component` добавляют поведение, `Scene` владеет сущностями и рассылает `update` / `fixed_update`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Порядок слоёв (зависимости только вниз): `main.py / player.py` → `editor/` → `plugins/` → `core/components/` → `core/renderer, physics, audio, network` → `core/ecs/` (`Entity`, `Component`, `Scene`, `ComponentRegistry`) → `core/foundation/` → `core/spatial, core/maths` (`ARCHITECTURE.md`). `core/` никогда не импортирует `editor` и `plugins`; связь вверх — только события `Engine.on` / `Engine.off` и колбэки `_emit_event`.

Рантайм-диспетч: `Engine.tick()` (только в play-режиме) выполняет до 5 фиксированных шагов (`Scene.fixed_update(fd)`), затем один `Scene.update(dt)`. `Scene` держит сеты `_active_update_components` / `_active_fixed_components` плюс кэшированные списки; `on_update` партиционируется на constraints → остальные → IK. Исключения в колбэках логируются с именами сущности и компонента и кадр не останавливают.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| Entity | class | `core/ecs/entity.py` | Идентичность + иерархия + хранилище компонентов |
| Component | class | `core/ecs/component.py` | Единица поведения/данных с хуками жизненного цикла |
| Scene | class | `core/ecs/scene.py` | Владелец сущностей, индекс компонентов, диспетч апдейтов, Octree spatial-индекс |
| ComponentRegistry | class | `core/ecs/registry.py` | Мапа имя → класс компонента, категории |
| public_import | statement | `from core.ecs import Component, Entity, Scene, ComponentRegistry` | Канонический импорт (`ARCHITECTURE.md`) |

## Использование в редакторе

Сущности создаются в панели Hierarchy; компоненты добавляются через `Add Component` (категории реестра: Transform, Rendering, Physics, Physics 2D, Lighting, Audio, Constraints, Network, Scripting). Инспектор редактирует поля; тогл `enabled` включает участие в апдейтах.

## Использование в коде

```python
from core.ecs import Component, Entity, Scene, ComponentRegistry
from core.components.transform.tr import Transform

scene = Scene("Demo")
player = scene.create_entity("Player")
player.add_component(Transform())
scene.start()          # Scene.start() -> on_start() on all components
scene.update(0.016)    # on_update(dt) on active, enabled components
scene.fixed_update(0.02)
```

## Сериализация

`Scene.serialize()` → `{"name": ..., "entities": {id: entity-dict}}`, опционально `embed_all`, `compress_resources`, `compress_level`. `Scene.deserialize(data, registry)` пересобирает сущности и перелинковывает родителей. Больше 64 сущностей — обе стороны через thread pool.

## Связанные страницы

- [Сущность Entity](./entity.md)
- [Компоненты и жизненный цикл](./components-lifecycle.md)
- [Реестр и сериализация](./registry-serialization.md)
- [Трансформ](./transform.md)
- [Время и Fixed Update](./time.md)

## Проверено по

- `ARCHITECTURE.md` (слои, модули, правила компонентов)
- `core/ecs/__init__.py`, `core/ecs/entity.py`, `core/ecs/component.py`, `core/ecs/scene.py`, `core/ecs/registry.py`, `core/ecs/query.py`, `core/ecs/scene_serialize.py`
- `core/engine/eng/loop.py` (`tick`, `tick_begin`, `tick_fixed_step`, `tick_update`)
