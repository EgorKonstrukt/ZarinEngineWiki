# Roadmap, Changelog, License

Facts only — everything below is read straight from the repository.

## License

- Mozilla Public License 2.0. Copyright (c) 2026 Zarrakun. Full text in `LICENSE` at the repository root.

## Requirements (from `README.md`)

- Python 3.13+, PySide6 ≥ 6.6.0, ModernGL ≥ 5.10.0, NumPy ≥ 1.26.0, system OpenAL for audio, bundled Assimp DLL/SO for mesh import. Full list in `requirements.txt`.
- OpenGL 4.6 Core Profile. All engine math is `numpy.float64`; `float32` conversion happens only at GPU upload.

## Entry points and tests

- `python main.py` — editor. `python player.py Scene.zpes` — run a scene (default `"SampleScene.zpes"`). `python build_nuitka.py` — Nuitka standalone build (settings in `BuildSettings.json`).
- `python setup.py` — compiles the 34 engine Cython extensions from `core/pyx/*.pyx`: `_convex_hull`, `_bvh_build`, `_raytracing_data`, `math_helpers`, `_math_vec`, `_culling`, `_transform_batch`, `_render_utils`, `_physics_utils`, `_core_batch`, `_types`, `_ecs_batch`, `_render_batch`, `_octree_batch`, `_constraint_batch`, `_curve_batch`, `_constraint_update`, `_physics_sync`, `_mesh_import`, `_skinning`, `_ik`, `_physbone`, `_audio_dsp`, `_raycast`, `_shadow_batch`, `_scene_query`, `_render_collect`, `_math_mat4`, `_vr_batch`, `_nav_batch`, `_splat_sort`, `_gs_volume`, `_sky_astro` (plus `_shader_parse` sources). Windows default toolchain is the auto-downloaded compact MinGW-w64.
- Smoke tests in `tests/smoke/`: `test_scripting`, `test_script_inspector`, `test_script_cython`, `test_engine_embed`, `test_relpaths`, `test_import_materials`, `test_mesh_import_persist`, `test_ik_numeric`, `test_blendshapes`, `test_compression_async`, `test_digest_dedup`, `test_duplicate_skinned`, `test_embed2`, `test_embed_outside`, `test_prop_picker`, `test_soft_remote`, plus `tests/libs/test_library_packs.py`.

## Changelog and roadmap

- No `CHANGELOG.md` ships with the repository: the git history is the change record. The scripting contract source of truth is `docs/scripting.md` in the engine repo.
- Verifiable direction from the code (not promises): gameplay-side networking is stubs (`host`/`connect`/`send`/`broadcast`/`poll` awaiting a transport; `NetworkIdentity` is a stub component), while ECS, editor, rendering, physics, audio, GUI, prefabs, scripting hot-reload and user Cython companions are implemented and covered by the smoke tests above.
