# Первая сцена

> Создайте сцену в коде или в редакторе: сущности с `Transform`, свет, камера, затем `save_scene_async` в `.zpes`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

`Engine.new_scene(name)` создаёт пустую `Scene` плюс locked-системную сущность `Shadow System` (держит `DirectionalShadow`, `PointShadow`, `SpotShadow`, `AreaShadow`). Каждая добавленная сущность получает `id` (UUID), `name`, `active=True`, `tags=set()`, `layer=0`. Сохранение — `save_scene_async(path)`; загрузка — `load_scene_async(path)` (оба в `EngineScenesMixin`; для скриптов есть синхронные `save_scene(path)` / `load_scene(path)`). Сохранения ротируют два бэкапа (`.bak`, `.bak2`).

## Свойства

| name | type | default | описание |
|---|---|---|---|
| new_scene | method | `Engine.new_scene(name="NewScene")` | Создаёт сцену, дефолтные объекты, emit `scene_loaded` |
| create_entity | method | `Scene.create_entity(name="Entity")` | Сущность, привязанная к сцене, помечена dirty |
| save | method | `Engine.save_scene_async(path)` | Асинхронное сохранение; emit `scene_saved`; `.bak`-бэкапы |
| load | method | `Engine.load_scene_async(path)` | Асинхронная загрузка с прогресс-таской `scene:load` |
| scene_file | format | `.zpes` JSON | `{"name": ..., "entities": {id: entity-dict}}` |

## Использование в редакторе

1. `File → New Scene` (или старт с `new_scene`).
2. Добавьте сущности в Hierarchy; двигайте гизмо вьюпорта (пишет `Transform.local_position` / `local_euler_angles` / `local_scale`).
3. Добавьте камеру и свет через `Add Component → Rendering / Lighting`.
4. Сохраните сцену как `.zpes`. Запустите headless через `python player.py MyScene.zpes`.

## Использование в коде

```python
from core.engine.eng import Engine
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3

engine = Engine()
engine.initialize()
scene = engine.new_scene("FirstScene")

player = scene.create_entity("Player")
tr = player.add_component(Transform())
tr.local_position = Vec3(0.0, 1.0, 0.0)

engine.save_scene_async("scenes/FirstScene.zpes")
```

## Сериализация

Минимальный фрагмент `.zpes`:

```json
{ "name": "FirstScene",
  "entities": {
    "c0ffee00-0000-4000-8000-000000000001": {
      "id": "c0ffee00-0000-4000-8000-000000000001",
      "name": "Player", "active": true, "tags": [], "layer": 0, "parent": null,
      "components": [
        { "type": "Transform", "enabled": true, "_key": "Transform",
          "local_position": [0.0, 1.0, 0.0],
          "local_rotation": [0.0, 0.0, 0.0, 1.0],
          "local_scale": [1.0, 1.0, 1.0] }
      ]
    }
  } }
```

## Связанные страницы

- [Быстрый старт](./quick-start.md)
- [Сущность Entity](../core/entity.md)
- [Трансформ](../core/transform.md)
- [Реестр и сериализация](../core/registry-serialization.md)
- [Сцены](../scenes-prefabs/scenes.md)

## Проверено по

- `core/engine/eng/engine.py` (`new_scene`, `_ensure_shadow_system`, `_add_default_scene_objects`)
- `core/engine/eng/scenes.py` (`save_scene_async`, `load_scene_async`, `_rotate_scene_backups`, `_SCENE_BACKUP_KEEP = 2`)
- `core/ecs/entity.py` (`Entity.__init__` defaults, `serialize`)
- `core/components/transform/tr/serialize.py` (ключи Transform)
