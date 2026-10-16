# Inspector

> The Inspector panel (`editor/inspector/`) — per-entity component editing: add/remove/reorder components, typed field widgets, copy/paste values, prefab apply/revert, animator state editors.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `InspectorClipboardMixin.copy_component` | `(comp_name: str, comp_key: str = "")` | `inspector/panelp/clipboard.py` |
| `InspectorClipboardMixin.paste_component_values` | `()` | Pastes buffered values onto the target |
| `InspectorClipboardMixin.clear_clipboard` | `()` | Drops the buffer |
| `InspectorUndoMixin._add_component` | `(comp_name: str)` | Undoable add + collab forward |
| `InspectorUndoMixin._add_script_component` | `(script_path: str)` | Attaches a script component |
| `InspectorUndoMixin._remove_component` | `(comp_name: str, comp_key: str = "")` | Undoable remove |
| `InspectorUndoMixin._move_component_up` / `_move_component_down` | `(comp_key: str)` | Reorder |
| `InspectorUndoMixin._show_add_component_menu` | `()` | Opens the picker |
| `InspectorUndoMixin._on_apply_prefab` / `_on_revert_prefab` | `()` | Prefab overrides commit/discard |
| `InspectorUndoMixin._on_active_changed` / `_on_name_changed` / `_on_tag_changed` / `_on_layer_changed` | `(checked: bool)` / `(text: str)` / `(val: int)` | Entity header fields |
| `ComponentPickerDialog` | `(entity, parent=None)` + `selected_result() -> Optional[dict]` | `inspector/component_picker.py`; category drill-down + search |
| `EntityPickerDialog` | `(scene, parent=None)` + `selected_id() -> Optional[str]` | `inspector/widgets.py` entity reference picker |
| `EntityPropertyTree` | `(scene, entity_id, ...)` + `set_property(path)`, `current_property()`, `property_label()` | `inspector/entity_property_picker.py` |
| `EntityPropertyDialog.exec_near_cursor` | `() -> int` + `result_entity_id/result_property/result_label` | Cursor-anchored property pick |
| `SourceViewerDialog` | `(file_path: str, line_number: int = 1, title="Source", parent=None)` | Read-only script jump |
| Animator editors | `_build_animator_state_inspector(state)`, `_build_animator_transition_inspector(trans)`, condition rows add/remove | In `inspector/panelp/undo.py` |
| Field widgets | `_FocusSpinBox`, `_DragLabel`, `_ResourceDropLabel`, `_EntityDropLabel` | Drag-to-tweak numbers, asset/entity drop targets |

## Usage in Editor

Add Component opens `ComponentPickerDialog` (categories + search); each component header offers copy/paste values, move up/down, remove. Entity fields accept drag-and-drop assets and entity references. Animator states/transitions get dedicated sub-editors with condition rows. Header toggles active flag, name, tag, layer. No editor hotkeys for these actions exist in code — all Inspector work is pointer-driven.

## Usage in Code

```python
inspector.copy_component("Transform", "")
inspector.paste_component_values()
dlg = ComponentPickerDialog(entity, parent=mw)
if dlg.exec():
    inspector._add_component(dlg.selected_result()["name"])
```

## Related

- Hierarchy (selection source), Project Panel (drag sources), Prefab editor panel
- Scripting: Inspector Fields (component attribute schema behind the widgets)

## Verified against

- `editor/inspector/panelp/clipboard.py`, `editor/inspector/panelp/undo.py`
- `editor/inspector/component_picker.py`, `editor/inspector/entity_property_picker.py`, `editor/inspector/widgets.py`, `editor/inspector/source_viewer.py`
- `editor/panels/add_entity_menu.py`
