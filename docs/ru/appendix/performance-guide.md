# Гид по производительности

> Рычаги, измеримо двигающие время кадра, с точным местом каждого в коде.

[Home](../../index.md)

## Профайлер

`core/foundation/profiler.py`: `Profiler(max_frames=300)` держит скользящий `deque(maxlen=300)` (`profiler.py:62-64`); `start/stop/scope/set_value` кормят словарь `_flat_data` + опциональные покадровые `ProfileSample(name, depth, start_ms, duration_ms, color)` (`profiler.py:74-123`); `capture_frame()` финализирует кадры (`profiler.py:125-150`); `enabled` и `capture_frames` — независимые тоглы (`profiler.py:170-207`). Дефолты панели: глобальные `profiler.update_interval=0.5`, `max_samples=200`, `refresh_interval=500` (`core/config/config.py:174-179`). Отслеживаемые ключи (цветовая карта, `profiler.py:15-39`, 40+ записей): `frame`, `tick`, `render_scene`, `render_ms`, `render_widget`, `fixed_update`, `update`, `scene_update`, `scene_fixed_update`, `scene_start`, `scene_serialize`, `scene_deserialize`, `physics_ms`, `PhysicsPlugin`, `physics_scan_bodies`, `physics_drain_results`, `physics_snapshot`, `physics_worker_send`, `physics_scene_step`, `phys_register`, `phys_sync_to_solver`, `phys_step_sim`, `phys_constrain_2d`, `phys_sync_to_ecs`, `phys_collision_events`, `animation_ms`, `audio_ms`, `ai_ms`, `particle_ms`, `ui_ms`, `editor_particles`, `input_handling`, `logic_update`, `frame_overhead`, `cam_update`, `gl_setup`, `gizmo_lines`, `gizmo_wireframes`, `gizmo_icons`, `gizmo_collab`, `collab_camera`, `overlay_draw`, `load_mesh`, `load_obj`. Экспорт flamegraph-SVG — внешний: `tools/profile_flamegraph.bat` / `profile_svg.bat` делают `py-spy record -o *.svg --subprocesses -- python main.py`; `profile_top.bat` — `py-spy top --subprocesses`.

## Каденс кадров

`rendering.vsync` (глобальный дефолт `True`) → `QSurfaceFormat.setSwapInterval(0/1)` и в `main.py:210-213`, и в `player.py:272-275`. `rendering.tick_rate=120.0` / `fixed_tick_rate=60.0` задают частоты `GameWorker` (`core/engine/eng/engine.py:395-399`); `engine.fixed_update_dt=0.02` — фиксированный шаг (`Engine` клампит к `>= 0.001`); `engine.time_scale=1.0` масштабирует логическое время; `MAX_FIXED_STEPS=5` ограничивает догон (`core/config/constants.py:12`). `rendering.target_fps=60`, `viewport.update_interval=16`, `viewport.overlay_fps=60`, `play_viewport_throttle="editor"` + `play_viewport_throttle_step=2` (глобальные дефолты rendering, `config.py:180-200`).

## Стоимость геометрии / шейдинга

- Вода: `mesh_resolution=128.0` (слайдер 2–400), `detail_octaves=6.0` (слайдер 1–12), `detail_scale=1.0`, `detail_fade=350.0` (`core/components/rendering/environment/waterp/component.py:70-74,120-125`); `_DetailOctaves` пушится в шейдер int-униформой (`waterp/surfacing.py:156`).
- Постfx объёмного дыма: `_base_octaves=4`, `_ridge_octaves=3`, `_detail_octaves=2` (каждый клампится 1–8 в `vsmoke/rendering.py:70-74`; инспектор 1–8, `vsmoke/inspector.py:21-23`).
- FBM-ноды графа террейна: `octaves` дефолт 9, диапазон 1–16 (`editor/terrain_graph/nodesp/generators.py:16,37,59`); CPU-дефолт `octaves: 9` (`core/terrain/terrain_generator.py:24`); порывы ветра `_fbm1d(t, octaves=4)`, `gust_octaves=4` (1–6, `wind_zone.py:16-70`).
- Тени: `DirectionalShadow._shadow_resolution=4096`, `SpotShadow=1024`, `PointShadow=512`, `AreaShadow=512` (каждый `…/environment/*_shadow.py:31-47`); проектный `rendering.shadow_distance=50.0`; глобальные `rendering.max_lights=8`, `light_scale=1.0`. LUT атмосферы: `_resolution_scale=1.0` (0.25–1.0, `atmosp/component.py:23,44`), `max(8, int(base*scale))` в `atmosp/compute.py:145`; `RadianceCascadesGI._resolution_scale=0.5` с quarter-res таргетами (`radiance/component.py:36-47`, `radiance/resources.py:57-58`); `DynamicCubemaps.resolution=128` (32–512, `dynacube/component.py:24,33`).

## Бюджет аудио

Глобальные `audio.max_sources=32`, `stream_buffer_size=4096` (`config.py:281-299`); проектное аудио дублирует громкости + `priority_threshold=0.1`, `doppler_factor=1.0`, `speed_of_sound=343.3` (`config.py:383-396`). DSP-хотпасы — Cython: `push_samples / mix_add / extract_last / analyze_spectrum` (`core/pyx/_audio_dsp.pyx:85-145`, собирается как `core._audio_dsp_cy`).

## Многопоточность

Проектный `physics.simulation_mode="multi_threaded"` по дефолту; солвер `culverin`: `culverin_max_bodies=65536`, `culverin_max_pairs=65536`, `culverin_max_contact_constraints=65536`, `culverin_temp_allocator_size=16777216`, `culverin_max_physics_jobs=0`, `culverin_max_physics_barriers=0`, `culverin_num_threads=0` (0 = авто), `culverin_enable_ccd=False`, `culverin_enable_sleeping=True` (`config.py:352-381`). Фастпас OBJ: потоки `_OBJ_FAST_THREADS` (`importer/settings.py:25`); in-memory кэш мешей — 12 записей (`_MEM_CACHE_MAX=12`, `settings.py:23`); кэш настроек текстур — 1024 записи (`texture_import_settings.py:100-103`). Сборка Cython параллелится по `os.cpu_count() or 4` (`build_cython.py:148`).

## Нативное ускорение (Cython)

33 расширения в `core/build_cython.py:96-130` (`EXTENSIONS`), директивы `boundscheck=False, wraparound=False, cdivision=True, infer_types=True` (`build_cython.py:136-147`); MinGW `-O2 -DNDEBUG -march=x86-64 -mtune=generic`, MSVC `/O2 /fp:fast /arch:AVX2 /GL /DNDEBUG`, Linux `-O3 -ffast-math -march=native -DNDEBUG -flto -fopenmp` (`build_cython.py:65-83`). Конвенция загрузки в GPU — `float32`: плоскости фрустума, гизмо/инстанс-буферы, скиннинг/трансформ/куллинг-аплоады — всё `np.float32` / `.astype(np.float32)` (напр. `renderer/culling.py:28-29`, `renderer/gpu_skinning.py:62-66`, `renderer/gpu_transform.py:54-56`). Математика `core/spatial`/`core/maths` — `numpy float64` по `ARCHITECTURE.md`.

## Проверено по

- `core/foundation/profiler.py` (300 скользящих кадров, ключи/цвета, тоглы), `core/config/config.py:174-200, 257-264, 281-299, 352-381` (все ручки выше)
- `main.py:210-213`, `player.py:272-275`, `core/engine/eng/engine.py:395-399`, `core/config/constants.py:12`
- `core/components/rendering/environment/waterp/component.py`, `surfacing.py`, `vsmoke/*`, `editor/terrain_graph/nodesp/generators.py`, `core/terrain/terrain_generator.py`, `environment/*_shadow.py`, `atmosp/*`, `radiance/*`, `dynacube/component.py`, `wind_zone.py`
- `core/pyx/_audio_dsp.pyx`, `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/build_cython.py`, `tools/profile_*.bat`, `ARCHITECTURE.md`
