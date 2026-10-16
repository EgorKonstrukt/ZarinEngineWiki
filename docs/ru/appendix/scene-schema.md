# Схема сцены (`.zpes`)

> JSON-раскладка сохранённой сцены: словари сущностей, словари компонентов, конвенции бэкап-ротации + shadow-системы. Все ключи ниже — буквальный вывод `serialize()`.

[Home](../../index.md)

## Конверт файла

Атомарная запись: JSON (`indent=2`) → temp `<сцена>.build` → `os.replace` (`core/engine/eng/scenes.py:55-59`). Перед каждым сейвом бэкапы ротируются с `_SCENE_BACKUP_KEEP = 2` (`scenes.py:20-52`): `*.bak2` старше keep удаляется, `*.bak` → `*.bak2`, текущий файл → `*.bak`. Т.е. `Scene.zpes.bak` — свежайшее поколение, `Scene.zpes.bak2` — на поколение старше. Эмбед ресурсов идёт до персиста (`_embed_scene_resources`, `scenes.py:63-70`); пути релятивизуются через `PATH_FIELDS` (`mesh_path`, `material_path`, `clip_path`, `script_path`, `texture_path`, `ply_path`, `shader_path`, `env_path`, `svg_path`, `font_path`, `video_path`, `graph_path`, `skybox_path` — `core/config/constants.py:14-18`).

## Словарь сущности (`core/ecs/entity.py:408-426`)

Всегда: `id`, `name`, `active`, `tags` (лист), `layer` (int), `parent` (id родителя или `None`), `components` (лист; каждая запись — словарь компонента плюс `_key`). Опционально, только когда задано: `prefab_guid`, `prefab_source_id`, `embed_resources: True`, `locked: True`, `system: True`. Десериализация зеркалит каждый ключ с дефолтами `active=True`, `tags=[]`, `layer=0` (`entity.py:428-445`).

```json
{
  "id": "…", "name": "Shadow System", "active": true, "tags": [], "layer": 0,
  "parent": null, "locked": true, "system": true,
  "components": [{ "type": "Transform", "_key": "Transform", "enabled": true }]
}
```

## Словари компонентов (выборочно)

Базовый `Component.serialize()` даёт `type` + `enabled` (см. `core/ecs/component.py:192`). Примеры:

- `ScriptComponent` (`core/components/scripting/scriptc/component.py:132-150`): добавляет `script_path` (str) + `script_fields` (dict; `Vec2/3/4` → листы, `Enum` → value, `Curve` → `to_dict()`, иначе raw). Десериализация восстанавливает `Curve` из любого dict с `keys` (`component.py:153-168`).
- `Rigidbody` (`core/components/physics/rigidbody.py:76-98`): `mass=1.0`, `drag=0.0`, `angular_drag=0.05`, `use_gravity=True`, `is_kinematic=False`, `freeze_pos=[x,y,z]`, `freeze_rot=[x,y,z]` bools.
- Тени (`…/environment/directional_shadow.py:76-89`, `spot_shadow.py:39-47`, `point_shadow.py:39-47`, `area_shadow.py:39-47`): `_shadow_resolution` (4096 / 1024 / 512 / 512).
- `TooltipComponent` (`controls/tooltip_component.py:59-72`): `text=""`, `_duration=0.0`, `_rich_text=True`.
- Вода (`waterp/component.py:136+`): `material_path`, `surface_type`, `infinite_ocean`, `ocean_size`, `waves[]`, `deep_color`, …, `mesh_resolution`, `detail_octaves`, …

## Сущность Shadow System

`Engine._ensure_shadow_system` (`core/engine/eng/engine.py:354-379`, вызывается также из `eng/scenes.py:160,229,352,378`): если в сцене нет сущности с `DirectionalShadow`, создаётся сущность `"Shadow System"` с `Transform + DirectionalShadow + PointShadow + SpotShadow + AreaShadow`, флаги `system=True`, `locked=True`. Locked/system-сущности скипаются пикингом вьюпорта (`viewport_camera.py:106`) и DnD иерархии (`panels/hierarchy/dnd.py:167`).

## Проверено по

- `core/ecs/entity.py:408-445` (точный набор ключей), `core/ecs/component.py:192`, `core/ecs/scene.py:361-414` (путь сериализации)
- `core/components/scripting/scriptc/component.py:132-168`, `core/components/physics/rigidbody.py:76-98`
- `core/engine/eng/scenes.py:20-70` (`.bak`/`.bak2`, атомарная запись `.build`, эмбед), `core/config/constants.py:14-18` (`PATH_FIELDS`)
- `core/engine/eng/engine.py:354-382` (Shadow System, locked+system), `editor/viewport/viewport_camera.py:106`, `editor/panels/hierarchy/dnd.py:167`
