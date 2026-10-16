# Справочник конфига

> Полная карта ключей/дефолтов `get_global_config()` (18 групп) и `get_project_config()` (5 групп). Глобальный файл: `~/.zarin/settings.json`; проектный: `<project>/ProjectSettings.json`.

[Home](../../index.md)

## Обзор

`Config(path, defaults)` (`core/config/config.py:14`) — глубокий мерж JSON поверх дефолтов. `get` — dot-нотация с дефолтом вызывающего при любом отсутствующем сегменте (`config.py:35-43`); `set` создаёт промежуточные словари и нотифицирует `on_changed`, если `notify` не `False` (`config.py:44-53`); `reset(key)` возвращает один ключ к дефолту, `reset()` — весь словарь (`config.py:56-59`). Устаревшие плоские цветовые ключи (`ambient_r`, `clear_r`, `bg_*`, `tri_*`, `wire_*`, `sky_*`, …) вычищаются при загрузке (`config.py:102-136`). Глобальный конфиг явно удаляет группу `physics` (`config.py:312`).

## Глобальный конфиг — 18 групп

Путь: `os.path.join(Path.home(), ".zarin", "settings.json")` (`config.py:142`).

| группа | ключи / дефолты |
|---|---|
| `editor` | `theme="dark"`, `font_size=12`, `ui_scale=75`, `language="en"`, `auto_save=True`, `auto_save_interval=300`, `thumb_cache_mode="metadata"`, `thumb_resolution=512`, `hover_delay=450` |
| `camera` | `fov=70.0`, `near=0.01`, `far=1000.0`, `move_speed=5.0`, `fast_mult=3.0`, `rotate_speed=0.3`, `zoom_speed=4.0`, `pan_speed=0.01`, `zoom_strength=0.3`, `damping=8.0`, `acceleration=12.0`, `transition_speed=2.5`, `zoom_smooth_speed=15.0`, `use_ortho_in_2d=True`, `speed_boost_enabled=True`, `speed_boost_mult=3.0`, `speed_boost_ramp_time=2.0` |
| `profiler` | `enabled=False`, `update_interval=0.5`, `max_samples=200`, `refresh_interval=500` |
| `rendering` | `vsync=True`, `target_fps=60`, `show_grid=True`, `grid_size=10.0`, `grid_world_size=2000.0`, `grid_2d_mode=False`, `grid_zoom_distance=5.0`, `ambient=[0.26,0.28,0.34]`, `selection_outline=[0.8,0.5,0.1,1.0]`, `selection_outline_thickness=0.03`, `tick_rate=120.0`, `fixed_tick_rate=60.0`, `max_lights=8`, `light_scale=1.0`, `play_viewport_throttle="editor"`, `play_viewport_throttle_step=2`, `bvh_build_mode="fast"`, `high_precision_rtc=False`, `high_precision_gpu=False` |
| `gizmo` | `handle_size=0.15`, `base_axis_length=1.5`, `plane_handle_size=0.22`, `pick_threshold=30.0`, `arrow_size_ratio=0.2`, `center_handle_size=0.14`, `screen_axis_length=100.0`, `line_width=2.5`, `show_delta_label=True`, `smooth_snap=False`, `smooth_snap_speed=0.25`, `show_icons=True`, `icon_scale=5.0`, `selection_bounds=True`, `selection_bounds_speed=13.0`, `selection_bounds_color=[1.0,0.0,1.0]`, `multigizmo_enabled=False`, `multigizmo_alignment="local"`, `multigizmo_orientation_lock=False`, `multigizmo_visible=True` |
| `console` | `font_size=10`, `font_family="Segoe UI"`, `max_blocks=2000`, `refresh_interval=250` |
| `terminal` | `font_size=10`, `font_family="Segoe UI"` |
| `viewport` | `clear=[0.18,0.18,0.18]`, `no_scene=[0.12,0.12,0.12]`, `update_interval=16`, `grid_step=10.0`, `overlay_fps=60` |
| `collab` | `cursor_rate=30.0`, `camera_rate=15.0`, `transform_rate=20.0`, `gizmo_rate=10.0`, `ping_interval=3.0`, `poll_interval=8`, `scene_sync_interval=2.0`, `relay_url="ws://127.0.0.1:8765"`, `auto_reconnect=True`, `heartbeat_timeout=30.0`, `upnp_enabled=True`, `upnp_lease=3600` |
| `undo` | `max_stack=200` |
| `engine` | `time_scale=1.0`, `fixed_update_dt=0.02`, `python_jit=False`, `python_optimize=0`, `python_unbuffered=False`, `python_no_bytecode=False` |
| `hierarchy` | `refresh_interval=2000` |
| `inspector` | `refresh_interval=250` |
| `project` | `thumb_size=64`, `dual_pane=True` |
| `plugins` | `allow_unsigned="warn"`, `registry_url=""`, `check_updates_on_startup=False`, `trusted_keys_dir=""` |
| `audio` | `enable_audio=True`, `device_name=""`, `sample_rate=48000`, `master_volume=1.0`, `sfx_volume=1.0`, `music_volume=0.8`, `voice_volume=1.0`, `ambient_volume=1.0`, `max_sources=32`, `stream_buffer_size=4096`, `distance_model="inverse_distance_clamped"`, `doppler_factor=1.0`, `speed_of_sound=343.3`, `enable_spatialization=True`, `enable_reverb=True`, `enable_occlusion=True`, `priority_threshold=0.1` |
| `file_assoc` | `registered_extensions=""` |
| `mesh_preview` | `camera_rot_x=30.0`, `camera_rot_y=-45.0`, `bg=[0,0,0,0]`, `tri=[0.39,0.63,0.86,0.16]`, `wire=[0.71,0.82,0.94,0.78]`, `wire_width=1.0` |

## Проектный конфиг — 5 групп

Путь: `<project_path>/ProjectSettings.json` (`config.py:320-321`).

| группа | ключи / дефолты |
|---|---|
| `project` | `name="Untitled"`, `version="1.0.0"`, `default_scene=""` (фолбэк плеера №3, `player.py:92-107`) |
| `input` | `control_scheme="fps"`, `horizontal="a,d"`, `vertical="w,s"`, `jump="space"`, `fire="mouse 0"`, `crouch="left ctrl"`, `sprint="left shift"`, `interact="e"`, `reload="r"`, `mouse_axis_x="mouse x"`, `mouse_axis_y="mouse y"`, `mouse_sensitivity=1.0`, `invert_mouse_x=False`, `invert_mouse_y=False`, `axis_gravity=3.0`, `axis_sensitivity=1.0`, `axis_dead=0.001` |
| `rendering` | `render_pipeline="forward"`, `anti_aliasing="none"`, `shadow_distance=50.0` |
| `physics` | `solver="culverin"`, `physx_device="cpu"`, `gravity_x=0.0`, `gravity_y=-9.81`, `gravity_z=0.0`, `fixed_time_step=0.02`, `num_sub_steps=2`, `solver_iterations=10`, `erp=0.2`, `contact_erp=0.2`, `friction_erp=0.0`, `contact_breaking_threshold=0.02`, `restitution=0.0`, `linear_damping=0.04`, `angular_damping=0.04`, `max_contacts_per_body=64`, `simulation_mode="multi_threaded"`, `culverin_max_bodies=65536`, `culverin_max_pairs=65536`, `culverin_max_contact_constraints=65536`, `culverin_temp_allocator_size=16777216`, `culverin_max_physics_jobs=0`, `culverin_max_physics_barriers=0`, `culverin_num_threads=0`, `culverin_penetration_slop=0.02`, `culverin_enable_ccd=False`, `culverin_enable_sleeping=True`, `layer_names=[16 имён]`, `collision_matrix=[16 int, дефолт 65535]` |
| `audio` | `master_volume=1.0`, `sfx_volume=1.0`, `music_volume=0.8`, `voice_volume=1.0`, `ambient_volume=1.0`, `enable_spatialization=True`, `enable_reverb=True`, `enable_occlusion=True`, `distance_model="inverse_distance_clamped"`, `doppler_factor=1.0`, `speed_of_sound=343.3`, `priority_threshold=0.1` |

## Restart-ключи (8)

Помечены через `mark_restart` (`config.py:313-316`); применяются при следующем запуске. `main.py:_apply_python_optimization` (`main.py:20-74`) перевыполняет интерпретатор при расхождении `engine.python_*` (флаги `-O`/`-OO`, env `PYTHON_JIT`, `PYTHONUNBUFFERED`, `PYTHONDONTWRITEBYTECODE`, guard `_ZARIN_OPT_REEXEC`):

`editor.theme`, `editor.font_size`, `editor.ui_scale`, `editor.language`, `engine.python_jit`, `engine.python_optimize`, `engine.python_unbuffered`, `engine.python_no_bytecode`.

## Использование в коде

```python
from core.config.config import get_global_config, get_project_config
cfg = get_global_config()
cfg.set("engine.time_scale", 0.5)
cfg.on_changed(lambda key, value: print(key, value))
cfg.save()  # pretty JSON, indent=2 (config.py:86-92)
print(cfg.is_restart_key("editor.theme"))  # True
cfg.reset("engine.time_scale")
```

## Проверено по

- `core/config/config.py` (все 18 глобальных групп + дефолты, 5 проектных групп + дефолты, restart-ключи, пути, `pop("physics", None)`, устаревшие ключи)
- `core/config/constants.py` (`MAX_FIXED_STEPS=5`, `PATH_FIELDS`, `LOGGER_MAX_ENTRIES=2000`)
- `main.py:20-74` (перезапуск с python-оптимизациями), `main.py:210-212` / `player.py:272-275` (`rendering.vsync` → swap interval)
- `player.py:92-107` (фолбэк `project.default_scene`)
