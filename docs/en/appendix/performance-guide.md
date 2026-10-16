# Performance Guide

> Levers that measurably move frame time, with the exact code location of each knob.

[Home](../../index.md)

## Profiler

`core/foundation/profiler.py`: `Profiler(max_frames=300)` keeps a rolling `deque(maxlen=300)` (`profiler.py:62-64`); `start/stop/scope/set_value` feed `_flat_data` dict + optional per-frame `ProfileSample(name, depth, start_ms, duration_ms, color)` capture (`profiler.py:74-123`); `capture_frame()` finalizes frames (`profiler.py:125-150`); `enabled` and `capture_frames` are independent toggles (`profiler.py:170-207`). Panel refresh defaults: global `profiler.update_interval=0.5`, `max_samples=200`, `refresh_interval=500` (`core/config/config.py:174-179`). Tracked keys (color map, `profiler.py:15-39`, 40+ entries): `frame`, `tick`, `render_scene`, `render_ms`, `render_widget`, `fixed_update`, `update`, `scene_update`, `scene_fixed_update`, `scene_start`, `scene_serialize`, `scene_deserialize`, `physics_ms`, `PhysicsPlugin`, `physics_scan_bodies`, `physics_drain_results`, `physics_snapshot`, `physics_worker_send`, `physics_scene_step`, `phys_register`, `phys_sync_to_solver`, `phys_step_sim`, `phys_constrain_2d`, `phys_sync_to_ecs`, `phys_collision_events`, `animation_ms`, `audio_ms`, `ai_ms`, `particle_ms`, `ui_ms`, `editor_particles`, `input_handling`, `logic_update`, `frame_overhead`, `cam_update`, `gl_setup`, `gizmo_lines`, `gizmo_wireframes`, `gizmo_icons`, `gizmo_collab`, `collab_camera`, `overlay_draw`, `load_mesh`, `load_obj`. Flamegraph SVG export is external: `tools/profile_flamegraph.bat` / `profile_svg.bat` run `py-spy record -o *.svg --subprocesses -- python main.py`; `profile_top.bat` runs `py-spy top --subprocesses`.

## Frame pacing

`rendering.vsync` (global default `True`) → `QSurfaceFormat.setSwapInterval(0/1)` in both `main.py:210-213` and `player.py:272-275`. `rendering.tick_rate=120.0` / `fixed_tick_rate=60.0` drive `GameWorker` update/fixed rates (`core/engine/eng/engine.py:395-399`); `engine.fixed_update_dt=0.02` is the fixed step (`Engine` clamps to `>= 0.001`); `engine.time_scale=1.0` scales logic time; `MAX_FIXED_STEPS=5` caps catch-up (`core/config/constants.py:12`). `rendering.target_fps=60`, `viewport.update_interval=16`, `viewport.overlay_fps=60`, `play_viewport_throttle="editor"` + `play_viewport_throttle_step=2` (global rendering defaults, `config.py:180-200`).

## Geometry / shading cost

- Water: `mesh_resolution=128.0` (slider 2–400), `detail_octaves=6.0` (slider 1–12), `detail_scale=1.0`, `detail_fade=350.0` (`core/components/rendering/environment/waterp/component.py:70-74,120-125`); `_DetailOctaves` is pushed to the shader as an int uniform (`waterp/surfacing.py:156`).
- Volumetric smoke postfx: `_base_octaves=4`, `_ridge_octaves=3`, `_detail_octaves=2` (each clamped 1–8 in `vsmoke/rendering.py:70-74`; inspector 1–8, `vsmoke/inspector.py:21-23`).
- Terrain graph FBM nodes: `octaves` default 9, range 1–16 (`editor/terrain_graph/nodesp/generators.py:16,37,59`); CPU default `octaves: 9` (`core/terrain/terrain_generator.py:24`); wind gusts `_fbm1d(t, octaves=4)`, `gust_octaves=4` (1–6, `wind_zone.py:16-70`).
- Shadows: `DirectionalShadow._shadow_resolution=4096`, `SpotShadow=1024`, `PointShadow=512`, `AreaShadow=512` (each `…/environment/*_shadow.py:31-47`); project `rendering.shadow_distance=50.0`; global `rendering.max_lights=8`, `light_scale=1.0`. Atmosphere LUT: `_resolution_scale=1.0` (0.25–1.0, `atmosp/component.py:23,44`), `max(8, int(base*scale))` in `atmosp/compute.py:145`; `RadianceCascadesGI._resolution_scale=0.5` with quarter-res targets (`radiance/component.py:36-47`, `radiance/resources.py:57-58`); `DynamicCubemaps.resolution=128` (32–512, `dynacube/component.py:24,33`).

## Audio budget

Global `audio.max_sources=32`, `stream_buffer_size=4096` (`config.py:281-299`); project audio mirrors volumes + `priority_threshold=0.1`, `doppler_factor=1.0`, `speed_of_sound=343.3` (`config.py:383-396`). DSP hot paths are Cython: `push_samples / mix_add / extract_last / analyze_spectrum` (`core/pyx/_audio_dsp.pyx:85-145`, built as `core._audio_dsp_cy`).

## Threading

Project `physics.simulation_mode="multi_threaded"` default; solver `culverin` with `culverin_max_bodies=65536`, `culverin_max_pairs=65536`, `culverin_max_contact_constraints=65536`, `culverin_temp_allocator_size=16777216`, `culverin_max_physics_jobs=0`, `culverin_max_physics_barriers=0`, `culverin_num_threads=0` (0 = auto), `culverin_enable_ccd=False`, `culverin_enable_sleeping=True` (`config.py:352-381`). Mesh OBJ fast path threads: `_OBJ_FAST_THREADS` (`importer/settings.py:25`); in-memory mesh cache holds 12 entries (`_MEM_CACHE_MAX=12`, `settings.py:23`); texture settings cache holds 1024 entries (`texture_import_settings.py:100-103`). Cython build parallelizes over `os.cpu_count() or 4` (`build_cython.py:148`).

## Native acceleration (Cython)

33 extensions in `core/build_cython.py:96-130` (`EXTENSIONS`), compiler directives `boundscheck=False, wraparound=False, cdivision=True, infer_types=True` (`build_cython.py:136-147`); MinGW flags `-O2 -DNDEBUG -march=x86-64 -mtune=generic`, MSVC `/O2 /fp:fast /arch:AVX2 /GL /DNDEBUG`, Linux `-O3 -ffast-math -march=native -DNDEBUG -flto -fopenmp` (`build_cython.py:65-83`). GPU upload convention is `float32`: frustum planes, gizmo/instance buffers, skinning/transform/culling uploads all use `np.float32` / `.astype(np.float32)` (e.g. `renderer/culling.py:28-29`, `renderer/gpu_skinning.py:62-66`, `renderer/gpu_transform.py:54-56`). Math in `core/spatial`/`core/maths` is `numpy float64` per `ARCHITECTURE.md`.

## Verified against

- `core/foundation/profiler.py` (300 rolling frames, keys/colors, toggles), `core/config/config.py:174-200, 257-264, 281-299, 352-381` (all knobs above)
- `main.py:210-213`, `player.py:272-275`, `core/engine/eng/engine.py:395-399`, `core/config/constants.py:12`
- `core/components/rendering/environment/waterp/component.py`, `surfacing.py`, `vsmoke/*`, `editor/terrain_graph/nodesp/generators.py`, `core/terrain/terrain_generator.py`, `environment/*_shadow.py`, `atmosp/*`, `radiance/*`, `dynacube/component.py`, `wind_zone.py`
- `core/pyx/_audio_dsp.pyx`, `core/assets/importer/settings.py`, `core/assets/texture_import_settings.py`, `core/build_cython.py`, `tools/profile_*.bat`, `ARCHITECTURE.md`
