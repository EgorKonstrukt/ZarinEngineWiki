# Viewport, Camera, Gizmos

> `SceneViewport` (`editor/viewport/viewport.py`) — the `QOpenGLWidget` scene view: camera control, picking/selection, transform gizmo, render and input mixins.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `SceneViewport` | `(engine, parent=None)` | Mixes `ViewportCameraMixin`, `ViewportOverlayMixin`, `ViewportSelectionMixin`, `ViewportRenderMixin`, `ViewportInputMixin` |
| `camera` / `gizmo` / `renderer` | properties | Sub-object accessors |
| `selected_entities` | `() -> list` | Current selection |
| `initializeGL` / `resizeGL` / `paintGL` | `(w, h)` / `()` | GL lifecycle |
| `load_config` | `(config) -> None` | Restores persisted viewport settings |
| `showEvent` / `hideEvent` | `(event)` | Start/stop of paint updates |
| `on_dock_top_level_changed` | `(floating: bool)` | Re-parent handling when undocked |
| Camera: `_on_fov_changed` / `_on_near_changed` / `_on_far_changed` | `(value: float)` | In `viewport/viewport_camera.py` |
| Camera: `_on_move_speed_changed` / `_on_rotate_speed_changed` | `(value: float)` | Fly/orbit speeds |
| Camera: `_on_render_scale_changed` | `(value: int)` | Render resolution scale |
| Camera: `set_grid_step` / `snap_to_grid` / `snap_vec3_to_grid` | `(step: float)` / `(value: float)` / `(v: Vec3)` | Grid snapping |
| Selection: `set_selected_entity` / `set_selected_entities` | `(entity)` / `(entities: list)` | In `viewport/viewport_selection.py` |
| Selection: `_delete_selected` / `_copy_selected_entities` / `_paste_entities` / `_frame_selected` | `()` | Edit ops on selection |
| Selection: `_emit_create_request` | `(obj_type="empty", subtype=None)` | Viewport create-menu entry point |
| Input keys | `KEY_W/A/S/D/Q/E/R/F`, `KEY_SHIFT/CTRL/ALT/SPACE/DELETE`, `MOUSE_L/R/M` from `core/input/constants.py` | In `viewport/viewport_input.py`; C/V/D handled for copy/paste/duplicate |
| `OverlayWidget` | `(viewport, parent=None)` + `set_visible(bool)` | `viewport/overlay_widget.py` |
| `ProgressToast` | `(parent=None)` + `reposition()` | `viewport/progress_toast.py` task toasts |

## Usage in Editor

Fly mode: `WASD` + right-mouse look, middle-mouse pan, wheel dolly. Orbit mode: right-mouse orbits around the 3D cursor, `Shift+RMB` click places the cursor, `Ctrl+MMB`/wheel dollies (toolbar nav-mode tooltip text). `C`/`V`/`D` operate on the selection (copy/paste/duplicate paths in the key handler). Gizmo follows `_update_gizmo_pivot`; multi-drag commits via `_commit_multi_transform`. FOV/near/far/move/rotate/render-scale/depth are edited through the camera controls wired to the `_on_*_changed` handlers.

## Usage in Code

```python
vp.set_selected_entity(ent)
vp.set_selected_entities([a, b])
vp.snap_vec3_to_grid(pos)   # snap a Vec3 to the viewport grid step
vp.set_grid_step(0.5)
```

## Screenshots

![ZarinEngine editor viewport (center) inside the main window](../../assets/screenshots/editor-main.png)

> Scene view with camera gizmo, grid, and dragon test model; right side is
> the Game view. File: `assets/screenshots/editor-main.png` (verified copy
> of engine `zarin_screenshot.png`).

## Related

- Main Window (toolbar hosts Play + nav mode), Hierarchy (selection mirror), Inspector (edits selection)
- Gizmos: `editor/gizmo/pb_scale_gizmo.py`, `editor/gizmo/phys_bone_grab.py`
- Play dock viewport: `editor/panels/play/viewport.py`

## Verified against

- `editor/viewport/viewport.py`, `editor/viewport/viewport_camera.py`, `editor/viewport/viewport_selection.py`, `editor/viewport/viewport_input.py`
- `editor/viewport/overlay_widget.py`, `editor/viewport/progress_toast.py`, `editor/viewport/perf.py`, `editor/viewport/projection.py`, `editor/viewport/undo_filter.py`
- `editor/main_window/toolbar.py` (nav tooltip), `editor/scene_viewport.py`, `editor/scene_toolbar.py`
