# Сцены

`Scene` — хранилище сущностей; `EngineScenesMixin` грузит/сохраняет. Связи префабов по GUID; оверрайды на инстансах.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `Scene.get_all_entities() / get_entity(id)` | `core/ecs/scene.py` | Доступ к сущностям |
| `Engine.load_scene(path) / load_scene_from_data(data)` | `core/engine/eng/scenes.py` | Десериализация JSON сцены |
| `Engine.save_scene(path) / save_scene_object(scene, path)` | `core/engine/eng/scenes.py` | Сериализация в JSON |
| `load_scene_async / save_scene_async` | `core/engine/eng/scenes.py` | Фоновые варианты с `on_done` |
| Поля сущности | `core/ecs/entity.py` | `id`, `name`, `active`, `parent`, `children`, `components`, `prefab_guid` |

## Использование в редакторе

1. Стройте иерархию; у каждой сущности стабильный `id`.
2. Сохранение пишет dict сущностей с компонентами и GUID префабов.
3. Загрузка пересоздаёт через `Entity.deserialize` + реестр, relink родителей.

## Использование в коде

```python
eng = Engine.instance()
scene = eng.load_scene("assets/scenes/level1.json")
eng.save_scene("assets/scenes/level1.json")
```

## Связанные

- Префабы и библиотека, ECS Entity

## Проверено по

- `core/engine/eng/scenes.py`, `core/ecs/scene.py`, `core/ecs/entity.py`
