# Physics Tooling Plugins

Four plugins around the physics core: simulation, debug visualisation, editor drag, and mesh editing.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `PhysicsPlugin` | `0.4.0` | `True` |
| `PhysicsVisualisation` | `1.1.0` | `True` |
| `PhysicsDragPlugin` | `0.1.0` | `False` |
| `MeshEditorPlugin` | `1.0.0` | `False` |

Declared in `plugins/physics_plug/plugin.py`, `plugins/physviz/plugin.py`, `plugins/physics_drag_plugin.py`, `plugins/mesh_editor_plugin.py`.

## What each does

**PhysicsPlugin** (`0.4.0`, SYSTEM) — the simulation itself (`DESCRIPTION = "Physics system with shared-memory parallel simulation."`). `simulation_mode` from project config: `multi_threaded` (default, `PhysicsProcess` shared-memory worker), `single` (in-process solver), `per_layer_process`. Solver resolved via `physics_solvers.registry` (`normalize`, `get_module_class`, `is_available`, fallback to `DEFAULT_SOLVER`). Key API: `physics_scene` property, `ensure_single_mode() -> bool`, hooks `on_scene_loaded` / `on_scene_unloaded` / `on_project_opened`. Note: `CharacterController` only works in-process — without it the controller logs `Use single-threaded physics mode (simulation_mode='single') for characters.`

**PhysicsVisualisation** (`1.1.0`, SYSTEM) — debug arrows through the `Gizmos` system (tag from `physviz/draw.py`): velocity / acceleration / moment vectors, contacts, joints, colliders. Settings (all live-patchable via `configure(patch)`): `enabled`, `show_velocity/acceleration/moment/contacts/joints/colliders`, `only_selected`, `show_labels`, `show_editor/show_runtime`, `velocity_scale` (0.3), `acceleration_scale` (0.05), `moment_scale` (0.05), `max_length` (2.5), `min_magnitude` (0.1), `smooth` (0.35), `accel_spike` (200.0), `moment_spike` (400.0), `label_cap` (48), `contact_normal_length` (0.6). Panel: dock `"Physics Visualisation"` (area `right`), menu `Tools → Physics Visualisation`, 50 ms `QTimer` refresh in editor, `step(dt)` redraw in play.

**PhysicsDragPlugin** (`0.1.0`, editor tool) — `Ctrl+Shift+LeftClick` drags simulated bodies in **play mode**. Installs an event filter on the viewport (`on_viewport_ready`); on press it casts `physics_scene.ray_cast(origin, direction, 1000.0)` and accepts only entities with a non-kinematic `Rigidbody` with `mass > 0`; move updates `update_drag_constraint(id, target)` (plane-projected via `viewport.screen_to_plane`), release calls `remove_drag_constraint(id)`. Scene handle is refreshed from `PhysicsPlugin.physics_scene` on `initialize` / `on_scene_loaded` / `on_play_start`.

**MeshEditorPlugin** (`1.0.0`, ProBuilder-style) — one menu item `Mesh Editor → Open Mesh Editor` (shortcut `Ctrl+Shift+M`) opening the main window's `_mesh_editor` for the first selected entity.

## Dragging a body — example

```python
# 1. Press Play (drag works only when engine.play_mode is True).
# 2. Hold Ctrl+Shift, left-click a dynamic Rigidbody in the viewport.
# 3. Move the mouse: the hit body follows on the grab plane
#    (debug line body -> anchor while dragging).
# 4. Release the mouse button: constraint removed, body keeps velocity.
#
# Script-side equivalent of what the filter does:
ps = engine.plugin_manager.get("PhysicsPlugin").physics_scene
origin, direction = viewport.screen_to_ray(x, y)
hit = ps.ray_cast(origin, direction, 1000.0)   # {"entity_id", "body_id", "position"}
cid = ps.create_drag_constraint(hit["body_id"], Vec3(*hit["position"]))
ps.update_drag_constraint(cid, new_target)
ps.remove_drag_constraint(cid)
```

## Verified against

`plugins/physics_plug/plugin.py`, `plugins/physviz/plugin.py`, `plugins/physics_drag_plugin.py`, `plugins/mesh_editor_plugin.py`, `core/components/physics/character/jump_slope.py` (single-mode warning).
