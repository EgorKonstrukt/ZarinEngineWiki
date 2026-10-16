# Импорт BSP-карт (BSPImport)

Импорт карт Source-движка: парсит геометрию `.bsp` и пересоздаёт её сущностями с материалами.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `BSPImport` | `2.0.0` | `False` |

Объявлен в `plugins/user/bsp_import/plugin.py` (`BSPImportPlugin`, `DESCRIPTION = "Import Source Engine BSP maps into the scene (async)"`), реэкспорт из `plugins/user/bsp_import/__init__.py`. Парсер: `plugins/user/bsp_import/parse.py` (`BSPFile`). Диалог настроек + запекание текстур: `plugins/user/bsp_import/build.py` (`ImportSettingsDialog`, `_generate_texture_png`, `BSPImportWorker`).

## Поток

`on_viewport_ready` вставляет `Tools → Import BSP...` → диалог файла (`BSP Files (*.bsp)`) → `ImportSettingsDialog.get_settings()` → `_import_sync(path, settings, engine, parent)`:

1. `BSPFile(path)` парсит лампы; в лог `BSP v{version}, {faces} faces, {verts} verts`.
2. `extract_mesh_groups(settings, progress_cb)` → группы `{name, vertices, normals, uvs, indices, centroid}`; группы с менее чем 3 индексами пропускаются.
3. Опциональное запекание (`generate_textures`, `texture_size` дефолт 128) → `assets/materials/bsp/<tex>.png`; `.mat`-файлы на группу (`{name, shader_path: "", properties: {albedo_texture, use_albedo_tex: 1}}`), если файла ещё нет.
4. Построение сцены: корневая сущность по имени файла + ребёнок на группу (`Transform` в `centroid`, `MeshFilter.mesh_name = f"bsp_{bsp}_{tex}"`, `MeshRenderer.materials[0]["path"] = <.mat>`), загрузка в GL через `mesh.build_gl(ctx, default_prog)`, если есть рендерер вьюпорта.

Ключи настроек: `merge_by_material` (дефолт True), `generate_textures` (True), `texture_size` (128), `center_pivot` (False), `convert_coords` (True), `scale` (1.0).

## Ограничения (из парсера)

- Принимаются только файлы с магией `VBSP` (иначе `ValueError: Not a valid BSP file`); в заголовке 32-битная версия и 64 записи ламп.
- Читаются только лампы: vertices (3), texdata (2), texinfo (6), faces (7), edges (12), surfedges (13), texdata string data (43) + table (44). Энтити, лайтмапы, видимость, физические халлы не импортируются.
- Ширина рёбер адаптируется под версию (32-битные индексы при `version >= 20` и валидных индексах, иначе 16-битные).
- Текстуры — процедурно сгенерированные PNG-заглушки по имени материала (`_generate_texture_png`); оригинальные встроенные/WAD-текстуры не извлекаются.
- Импорт синхронный (`_import_sync`); асинхронный путь `BSPImportWorker` существует в `build.py` вместе со сборщиком `_on_import_finished`.

## Пример

```python
# Путь через редактор (без кода): Tools -> Import BSP... -> выбрать de_dust2.bsp
#   -> оставить merge_by_material + generate_textures -> Import.
# Результат: сущность "de_dust2" с ребёнком на группу материалов,
# материалы в assets/materials/bsp/, иерархия обновлена.
from plugins.user.bsp_import.parse import BSPFile
bsp = BSPFile("C:/maps/de_dust2.bsp")
print(bsp.version, len(bsp.faces), len(bsp.vertices))
groups = bsp.extract_mesh_groups({"merge_by_material": True}, lambda p, m: None)
```

## Проверено по коду

`plugins/user/bsp_import/__init__.py`, `plugins/user/bsp_import/plugin.py`, `plugins/user/bsp_import/parse.py`, `plugins/user/bsp_import/build.py`.
