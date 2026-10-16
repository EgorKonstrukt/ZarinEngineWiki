# BSP Map Import (BSPImport)

Source-engine map import: parses `.bsp` geometry and rebuilds it as entities with materials.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `BSPImport` | `2.0.0` | `False` |

Declared in `plugins/user/bsp_import/plugin.py` (`BSPImportPlugin`, `DESCRIPTION = "Import Source Engine BSP maps into the scene (async)"`), re-exported from `plugins/user/bsp_import/__init__.py`. Parser: `plugins/user/bsp_import/parse.py` (`BSPFile`). Settings dialog + texture baking: `plugins/user/bsp_import/build.py` (`ImportSettingsDialog`, `_generate_texture_png`, `BSPImportWorker`).

## Flow

`on_viewport_ready` inserts `Tools → Import BSP...` → file dialog (`BSP Files (*.bsp)`) → `ImportSettingsDialog.get_settings()` → `_import_sync(path, settings, engine, parent)`:

1. `BSPFile(path)` parses lumps; logs `BSP v{version}, {faces} faces, {verts} verts`.
2. `extract_mesh_groups(settings, progress_cb)` → groups `{name, vertices, normals, uvs, indices, centroid}`; groups with fewer than 3 indices are skipped.
3. Optional texture baking (`generate_textures`, `texture_size` default 128) → `assets/materials/bsp/<tex>.png`; per-group `.mat` files (`{name, shader_path: "", properties: {albedo_texture, use_albedo_tex: 1}}`) unless one already exists.
4. Scene build: root entity named after the file + one child per group (`Transform` at `centroid`, `MeshFilter.mesh_name = f"bsp_{bsp}_{tex}"`, `MeshRenderer.materials[0]["path"] = <.mat>`), GL upload via `mesh.build_gl(ctx, default_prog)` when a viewport renderer exists.

Settings keys: `merge_by_material` (default True), `generate_textures` (True), `texture_size` (128), `center_pivot` (False), `convert_coords` (True), `scale` (1.0).

## Limitations (from the parser)

- Only files with `VBSP` magic are accepted (`ValueError: Not a valid BSP file` otherwise); header stores a 32-bit version and 64 lump entries.
- Only these lumps are read: vertices (3), texdata (2), texinfo (6), faces (7), edges (12), surfedges (13), texdata string data (43) + table (44). Entities, lightmaps, visibility, physics hulls are not imported.
- Edge width adapts to file version (32-bit indices when `version >= 20` and indices validate, else 16-bit).
- Textures are procedurally generated placeholder PNGs per material name (`_generate_texture_png`) — original embedded/WAD textures are not extracted.
- Import is synchronous (`_import_sync`); the async `BSPImportWorker` path exists in `build.py` alongside the `_on_import_finished` assembler.

## Example

```python
# Editor path (no code): Tools -> Import BSP... -> pick de_dust2.bsp
#   -> keep merge_by_material + generate_textures -> Import.
# Result: entity "de_dust2" with one child per material group,
# materials under assets/materials/bsp/, hierarchy refreshed.
from plugins.user.bsp_import.parse import BSPFile
bsp = BSPFile("C:/maps/de_dust2.bsp")
print(bsp.version, len(bsp.faces), len(bsp.vertices))
groups = bsp.extract_mesh_groups({"merge_by_material": True}, lambda p, m: None)
```

## Verified against

`plugins/user/bsp_import/__init__.py`, `plugins/user/bsp_import/plugin.py`, `plugins/user/bsp_import/parse.py`, `plugins/user/bsp_import/build.py`.
