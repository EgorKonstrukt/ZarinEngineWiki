# Реестр и сериализация

> `ComponentRegistry` мапит имена типов на классы компонентов через декоратор `@ComponentRegistry.register`. Сцены сериализуются в `.zpes` JSON: `{"name", "entities": {id: entity-dict}}`.

[Home](../../index.md) | [Section index](./_section.md)

## Обзор

Регистрация обязательна: недекорированный класс компонента невидим для меню `Add Component` и для десериализации (`ARCHITECTURE.md`: это проверяет architecture gate). `ComponentRegistry.get(name)` резолвит класс с учётом `_aliases` (`{"Sky": "ProceduralSky"}`). Категории выводятся из пути модуля (`core.components.<sub>` → отображаемое имя через `_category_name_map`: transform→Transform, rendering→Rendering, physics→Physics, physics2d→Physics 2D, lighting→Lighting, audio→Audio, constraints→Constraints, network→Network, scripting→Scripting).

Сериализация сцены (`core/ecs/scene_serialize.py`): `{"name", "entities": {id: entity-dict}}` плюс опциональные `embed_all`, `compress_resources`, `compress_level`. Десериализация пересобирает сущности, затем перелинковывает `parent`-id и перерегистрирует компоненты в индексе сцены. Больше 64 сущностей — обе стороны через thread pool. `duplicate_entity` / `paste_entities` выдают свежие UUID и перебиндивают `Armature.bone_entity_ids`.

## Свойства

| name | type | default | описание |
|---|---|---|---|
| ComponentRegistry.register | decorator | required | `ComponentRegistry._registry[name] = cls`; бампает кэш подклассов |
| ComponentRegistry.get | method | `get(name)` | Класс или `None`; резолвит `Sky` → `ProceduralSky` |
| ComponentRegistry.all | method | `all()` | Копия мапы имя → класс |
| get_categories | method | per component | напр. `["Transform"]` для `Transform` |
| Scene.serialize | method | dict | `name`, `entities`, опциональные embed/compress-флаги |
| Scene.deserialize | classmethod | `deserialize(data, registry)` | Пересборка + перелинковка родителей + синхронизация индекса |
| PATH_FIELDS | frozenset | `core/config/constants.py` | `mesh_path, material_path, clip_path, script_path, texture_path, ply_path, shader_path, env_path, svg_path, font_path, video_path, graph_path, skybox_path` — резолвятся от `project_root` при загрузке, релятивизируются при сохранении |

## Использование в редакторе

Группы `Add Component` — из `get_categories`. Неизвестные типы компонентов в `.zpes`-файле молча пропускаются при загрузке (остальное у сущности сохраняется). Инстансы префабов несут `prefab_guid` / `prefab_source_id`; оверрайды сериализуются рядом с GUID.

## Использование в коде

```python
from core.ecs import Component, ComponentRegistry

@ComponentRegistry.register
class Health(Component):
    pass

assert ComponentRegistry.get("Health") is Health
print(ComponentRegistry.get_categories("Health"))  # category by module path

data = scene.serialize()
scene2 = Scene.deserialize(data, ComponentRegistry)
```

## Сериализация

Полный скелет `.zpes` (ключи сущности: `id`, `name`, `active`, `tags`, `layer`, `parent`, `components`):

```json
{ "name": "MyScene",
  "entities": {
    "<uuid>": { "id": "<uuid>", "name": "E", "active": true, "tags": [],
                "layer": 0, "parent": null, "components": [] }
  } }
```

## Связанные страницы

- [Обзор ECS](./ecs-overview.md)
- [Сущность Entity](./entity.md)
- [Компоненты и жизненный цикл](./components-lifecycle.md)
- [Сцены](../scenes-prefabs/scenes.md)
- [Префабы](../scenes-prefabs/prefabs.md)

## Проверено по

- `core/ecs/registry.py` (`register`, `get`, `all`, `_aliases`, `_category_name_map`, `_infer_category`)
- `core/ecs/scene_serialize.py` (`_serialize_inner`, `_deserialize_inner`, `_duplicate_entity_inner`, `_rebind_armatures`)
- `core/ecs/entity.py` (`serialize`/`deserialize`, пропуск неизвестных типов)
- `core/config/constants.py` (`PATH_FIELDS`)
- `core/engine/eng/engine.py` (`resolve_scene_paths`, `relativize_scene_paths`)
- `ARCHITECTURE.md` (декоратор register обязателен)
