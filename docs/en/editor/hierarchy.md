# Hierarchy

> `HierarchyPanel` (`editor/panels/hierarchy/panel.py`) — the `QDockWidget` entity tree: search, expand state, prefab badges, create/reparent/delete, mirrored selection.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `HierarchyPanel` | `(engine: Engine, parent=None)` | Mixes `HierarchyDndMixin`, `HierarchyMenuMixin` |
| `refresh` / `_refresh` | `()` | Rebuilds the tree, preserves expansion |
| `load_config` | `(config) -> None` | Restores persisted panel state |
| `_on_scene_loaded` | `(scene: Scene)` | Rebind on scene switch |
| `_on_search` | `(text: str)` | Live filter (`_entity_matches_filter`) |
| `_collapse_all` / `_toggle_embed_all` / `_toggle_compress` | `()` / `(checked: bool)` | Tree display toggles |
| `_on_item_double_click` / `_on_item_changed` | `(item: QTreeWidgetItem, col: int)` | Rename flow |
| `set_selected_entity` / `set_selected_entities` | `(entity)` / `(entities: list)` | Selection API (dnd mixin) |
| `reveal_entity` / `flash_entity` | `(eid: str)` | Locate + highlight |
| `_frame_selected` | `()` | Focus viewport on selection |
| `HierarchyTree` | `(panel: HierarchyPanel, parent=None)` in `tree.py` | `QTreeWidget` with drag/drop MIME, `dropEvent`, `keyPressEvent` |
| Menu: `_create_entity` / `_create_child` / `_create_primitive` / `_create_probuilder_primitive` / `_create_light` / `_create_camera` / `_create_from_component` | see `menu.py` | Creation entries |
| Menu: `_save_prefab` / `_apply_prefab` / `_revert_prefab` / `_unpack_prefab` | `(entity: Entity)` | Prefab ops |
| DnD: `_on_reparent` / `_on_component_drop` / `_on_copy` / `_on_paste` / `_duplicate_entity` / `_delete_entity` | see `dnd.py` | Reparent, copy/paste, duplicate, delete |
| DnD: `_toggle_active` / `_toggle_lock` / `_toggle_embed` / `_toggle_active_selected` | see `dnd.py` | State toggles |
| Collab hooks: `_collab_sync_create` / `_collab_sync_delete` | `(entity)` / `(entity_id: str)` | Forward local edits to the session |

## Usage in Editor

Search box filters by name (`_entity_matches_filter`); double-click renames; context menu creates empty/child/primitives/lights/camera/component-based entities and runs prefab save/apply/revert/unpack. Drag re-parents (cycle-guarded by `_is_descendant`); dropping a component copies or moves it (`_on_component_drop`). `Delete` key path goes through `HierarchyTree.keyPressEvent`; copy/paste/duplicate preserve selection via `_restore_selection`. Prefab instances show `_prefab_mark` badges computed with an override cache.

## Usage in Code

```python
panel.refresh()
panel.set_selected_entity(ent)
panel.reveal_entity(ent.id)
panel.flash_entity(ent.id)
```

## Related

- Inspector (edits the selected entity), Viewport (selection + framing), Project Panel (prefab assets)
- Collaboration (create/delete forwarded to peers), Undo (history refresh hook `_on_history_changed`)

## Verified against

- `editor/panels/hierarchy/panel.py`, `editor/panels/hierarchy/tree.py`, `editor/panels/hierarchy/menu.py`, `editor/panels/hierarchy/dnd.py`
