# Project Panel and Assets

> `ProjectPanel` (`editor/panels/project/`) — the asset browser: folder tree + file list/detail views, drag-and-drop, file ops with undo, asset creation wizards, VCS hooks, thumbnail cache.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| Views | `FileListWidget`, `FileDetailWidget`, `FolderTreeWidget` in `project/widgets.py` | Icon list, detail tree, folder tree; all accept drops |
| `DndMixin._move_or_copy_files` | `(paths, dest_dir=None, force_move=None)` | Core drop/move engine |
| `DndMixin` file ops | `_copy_selected/_cut_selected/_paste_clipboard/_duplicate_selected/_delete_selected/_copy_path` | Clipboard + delete |
| `DndMixin._on_entity_drop` | `(event)` | Drops onto entities (material assign path) |
| `FsOpsMixin._fs_copy/_fs_move/_fs_remove` | `(src, dst) -> bool` | Guarded filesystem primitives |
| `FsOpsMixin` file undo | `_push_file_undo(op)`, `_undo_file_op()`, `_redo_file_op()` | Undoable file operations |
| `FsOpsMixin._rename_item` | `(widget, item)` | Inline rename |
| `FsOpsMixin` dir sizes | `_ensure_size_thread/_dir_size_loop/_request_dir_sizes/_on_dir_size_ready/_refresh_status_sizes` | Background size computation |
| `CreationMixin` creators | `_create_new_folder/_create_new_scene/_create_new_material/_create_new_physic_material/_create_new_animclip/_create_new_animcontroller/_create_new_script/_create_new_cython_script` | New-asset wizards |
| `CreationMixin.set_project_root` | `(path: str)` | Rebinds the browser root |
| `VcsMixin` | `project/vcs.py` | Version-control hooks surface |
| Thumbnails | `editor/thumb_cache.py`, `editor/thumb_worker.py` | Async thumbnail generation |

## Usage in Editor

Three panes: folder tree (navigate + drop target), file list (icons), detail view (columns). Drag files between folders to move/copy; drag assets into the viewport or onto entities to instantiate/assign. Right-click creates Folder, Scene, Material, Physic Material, Animation Clip, Animation Controller, Script, Cython Script. File copy/move/delete go through `_push_file_undo`, so they participate in undo. The breadcrumb bar (`_AddressBreadcrumb`) toggles between crumb and edit mode; the search bar filters the current folder. No letter hotkeys are bound in code for these views — operations are menu/drag-driven (views handle their own `keyPressEvent` for list navigation).

## Usage in Code

```python
panel.set_project_root(project_root)
panel._create_new_script()
panel._undo_file_op()   # undo last file operation
panel._redo_file_op()
```

## Related

- Hierarchy (prefab assets instantiated from here), Inspector (drop targets), Main Window (double-click routing)
- Thumbnail cache/worker, File Associations (`editor/file_associations.py`), OS shell menu (`editor/shellmenu/`)

## Verified against

- `editor/panels/project/widgets.py`, `editor/panels/project/dnd.py`, `editor/panels/project/fs_ops.py`, `editor/panels/project/creation.py`, `editor/panels/project/vcs.py`
- `editor/thumb_cache.py`, `editor/thumb_worker.py`
