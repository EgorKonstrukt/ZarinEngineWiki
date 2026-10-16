# Планы, журнал изменений, лицензия

Только факты — всё ниже прочитано напрямую из репозитория.

## Лицензия

- Mozilla Public License 2.0. Copyright (c) 2026 Zarrakun. Полный текст — `LICENSE` в корне репозитория.

## Требования (из `README.md`)

- Python 3.13+, PySide6 ≥ 6.6.0, ModernGL ≥ 5.10.0, NumPy ≥ 1.26.0, системный OpenAL для аудио, bundled Assimp DLL/SO для импорта мешей. Полный список — `requirements.txt`.
- OpenGL 4.6 Core Profile. Вся математика движка — `numpy.float64`; конвертация в `float32` только при загрузке на GPU.

## Точки входа и тесты

- `python main.py` — редактор. `python player.py Scene.zpes` — запуск сцены (дефолт `"SampleScene.zpes"`). `python build_nuitka.py` — standalone-сборка на Nuitka (настройки в `BuildSettings.json`).
- `python setup.py` — сборка 34 движковых Cython-расширений из `core/pyx/*.pyx`: `_convex_hull`, `_bvh_build`, `_raytracing_data`, `math_helpers`, `_math_vec`, `_culling`, `_transform_batch`, `_render_utils`, `_physics_utils`, `_core_batch`, `_types`, `_ecs_batch`, `_render_batch`, `_octree_batch`, `_constraint_batch`, `_curve_batch`, `_constraint_update`, `_physics_sync`, `_mesh_import`, `_skinning`, `_ik`, `_physbone`, `_audio_dsp`, `_raycast`, `_shadow_batch`, `_scene_query`, `_render_collect`, `_math_mat4`, `_vr_batch`, `_nav_batch`, `_splat_sort`, `_gs_volume`, `_sky_astro` (плюс исходники `_shader_parse`). Дефолтный тулчейн Windows — автодоставленный компактный MinGW-w64.
- Smoke-тесты в `tests/smoke/`: `test_scripting`, `test_script_inspector`, `test_script_cython`, `test_engine_embed`, `test_relpaths`, `test_import_materials`, `test_mesh_import_persist`, `test_ik_numeric`, `test_blendshapes`, `test_compression_async`, `test_digest_dedup`, `test_duplicate_skinned`, `test_embed2`, `test_embed_outside`, `test_prop_picker`, `test_soft_remote`, плюс `tests/libs/test_library_packs.py`.

## Журнал изменений и планы

- `CHANGELOG.md` в репозитории нет: записью изменений служит история git. Источник правды по контракту скриптинга — `docs/scripting.md` в репозитории движка.
- Проверяемое направление из кода (не обещания): геймплейная сеть — стабы (`host`/`connect`/`send`/`broadcast`/`poll` ждут транспорт; `NetworkIdentity` — заглушка), а ECS, редактор, рендеринг, физика, аудио, GUI, префабы, hot-reload скриптов и пользовательские Cython-модули реализованы и покрыты перечисленными smoke-тестами.
