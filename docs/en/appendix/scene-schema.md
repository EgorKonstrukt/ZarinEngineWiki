# Scene Schema (`.zpes`)

> JSON layout of a saved scene: entity dicts, component dicts, and the backup/rotation + shadow-system conventions. All keys below are the literal `serialize()` output.

[Home](../../index.md)

## File envelope

Atomic write: JSON (`indent=2`) → `<scene>.build` temp → `os.replace` (`core/engine/eng/scenes.py:55-59`). Before every save, backups rotate with `_SCENE_BACKUP_KEEP = 2` (`scenes.py:20-52`): `*.bak2` older than keep deleted, `*.bak` → `*.bak2`, current file → `*.bak`. So `Scene.zpes.bak` is the newest generation, `Scene.zpes.bak2` one older. Resource embedding runs before persist (`_embed_scene_resources`, `scenes.py:63-70`); paths relativized via `PATH_FIELDS` (`mesh_path`, `material_path`, `clip_path`, `script_path`, `texture_path`, `ply_path`, `shader_path`, `env_path`, `svg_path`, `font_path`, `video_path`, `graph_path`, `skybox_path` — `core/config/constants.py:14-18`).

## Entity dict (`core/ecs/entity.py:408-426`)

Always present: `id`, `name`, `active`, `tags` (list), `layer` (int), `parent` (parent id or `None`), `components` (list; each entry is the component dict plus `_key`). Optional, only when set: `prefab_guid`, `prefab_source_id`, `embed_resources: True`, `locked: True`, `system: True`. Deserialize mirrors every key with defaults `active=True`, `tags=[]`, `layer=0` (`entity.py:428-445`).

```json
{
  "id": "…", "name": "Shadow System", "active": true, "tags": [], "layer": 0,
  "parent": null, "locked": true, "system": true,
  "components": [{ "type": "Transform", "_key": "Transform", "enabled": true }]
}
```

## Component dicts (selected)

Base `Component.serialize()` contributes `type` + `enabled` (see `core/ecs/component.py:192`). Examples:

- `ScriptComponent` (`core/components/scripting/scriptc/component.py:132-150`): adds `script_path` (str) + `script_fields` (dict; `Vec2/3/4` → lists, `Enum` → value, `Curve` → `to_dict()`, else raw). Deserialize restores `Curve` from any dict with `keys` (`component.py:153-168`).
- `Rigidbody` (`core/components/physics/rigidbody.py:76-98`): `mass=1.0`, `drag=0.0`, `angular_drag=0.05`, `use_gravity=True`, `is_kinematic=False`, `freeze_pos=[x,y,z]`, `freeze_rot=[x,y,z]` bools.
- Shadows (`…/environment/directional_shadow.py:76-89`, `spot_shadow.py:39-47`, `point_shadow.py:39-47`, `area_shadow.py:39-47`): `_shadow_resolution` (4096 / 1024 / 512 / 512).
- `TooltipComponent` (`controls/tooltip_component.py:59-72`): `text=""`, `_duration=0.0`, `_rich_text=True`.
- Water (`waterp/component.py:136+`): `material_path`, `surface_type`, `infinite_ocean`, `ocean_size`, `waves[]`, `deep_color`, …, `mesh_resolution`, `detail_octaves`, …

## Shadow System entity

`Engine._ensure_shadow_system` (`core/engine/eng/engine.py:354-379`, also called from `eng/scenes.py:160,229,352,378`): if no `DirectionalShadow` entity exists in the scene, creates entity `"Shadow System"` with `Transform + DirectionalShadow + PointShadow + SpotShadow + AreaShadow`, flags `system=True`, `locked=True`. Locked/system entities are skipped by viewport picking (`viewport_camera.py:106`) and hierarchy DnD (`panels/hierarchy/dnd.py:167`).

## Verified against

- `core/ecs/entity.py:408-445` (exact key set), `core/ecs/component.py:192`, `core/ecs/scene.py:361-414` (serialize path)
- `core/components/scripting/scriptc/component.py:132-168`, `core/components/physics/rigidbody.py:76-98`
- `core/engine/eng/scenes.py:20-70` (`.bak`/`.bak2`, `.build` atomic write, embed), `core/config/constants.py:14-18` (`PATH_FIELDS`)
- `core/engine/eng/engine.py:354-382` (Shadow System, locked+system), `editor/viewport/viewport_camera.py:106`, `editor/panels/hierarchy/dnd.py:167`
