# Инструменты физики

Четыре плагина вокруг физического ядра: симуляция, отладочная визуализация, перетаскивание в редакторе и mesh-редактор.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `PhysicsPlugin` | `0.4.0` | `True` |
| `PhysicsVisualisation` | `1.1.0` | `True` |
| `PhysicsDragPlugin` | `0.1.0` | `False` |
| `MeshEditorPlugin` | `1.0.0` | `False` |

Объявлены в `plugins/physics_plug/plugin.py`, `plugins/physviz/plugin.py`, `plugins/physics_drag_plugin.py`, `plugins/mesh_editor_plugin.py`.

## Что делает каждый

**PhysicsPlugin** (`0.4.0`, SYSTEM) — сама симуляция (`DESCRIPTION = "Physics system with shared-memory parallel simulation."`). `simulation_mode` из конфига проекта: `multi_threaded` (дефолт, воркер `PhysicsProcess` на shared-memory), `single` (внутрипроцессный солвер), `per_layer_process`. Солвер — через `physics_solvers.registry` (`normalize`, `get_module_class`, `is_available`, откат на `DEFAULT_SOLVER`). Ключевое API: свойство `physics_scene`, `ensure_single_mode() -> bool`, хуки `on_scene_loaded` / `on_scene_unloaded` / `on_project_opened`. Важно: `CharacterController` работает только внутри процесса — иначе в логе `Use single-threaded physics mode (simulation_mode='single') for characters.`

**PhysicsVisualisation** (`1.1.0`, SYSTEM) — отладочные стрелки через систему `Gizmos` (тег из `physviz/draw.py`): векторы скорости / ускорения / момента, контакты, джойнты, коллайдеры. Настройки (все патчатся живьём через `configure(patch)`): `enabled`, `show_velocity/acceleration/moment/contacts/joints/colliders`, `only_selected`, `show_labels`, `show_editor/show_runtime`, `velocity_scale` (0.3), `acceleration_scale` (0.05), `moment_scale` (0.05), `max_length` (2.5), `min_magnitude` (0.1), `smooth` (0.35), `accel_spike` (200.0), `moment_spike` (400.0), `label_cap` (48), `contact_normal_length` (0.6). Панель: док `"Physics Visualisation"` (область `right`), меню `Tools → Physics Visualisation`, в редакторе обновление по `QTimer` 50 мс, в Play — `step(dt)`.

**PhysicsDragPlugin** (`0.1.0`, инструмент редактора) — перетаскивание тел `Ctrl+Shift+левая кнопка` в режиме **Play**. Ставит фильтр событий на вьюпорт (`on_viewport_ready`); по клику кастит `physics_scene.ray_cast(origin, direction, 1000.0)` и принимает только сущности с некинематическим `Rigidbody` массой `> 0`; движение обновляет `update_drag_constraint(id, target)` (проекция на плоскость через `viewport.screen_to_plane`), отпуск — `remove_drag_constraint(id)`. Хендл сцены обновляется из `PhysicsPlugin.physics_scene` в `initialize` / `on_scene_loaded` / `on_play_start`.

**MeshEditorPlugin** (`1.0.0`, в стиле ProBuilder) — один пункт `Mesh Editor → Open Mesh Editor` (хоткей `Ctrl+Shift+M`), открывающий `_mesh_editor` главного окна для первой выбранной сущности.

## Перетаскивание тела — пример

```python
# 1. Нажмите Play (перетаскивание работает только при engine.play_mode == True).
# 2. Зажмите Ctrl+Shift, кликните левой кнопкой по динамическому Rigidbody во вьюпорте.
# 3. Ведите мышь: тело следует за точкой на плоскости захвата
#    (во время драга рисуется debug-линия тело -> якорь).
# 4. Отпустите кнопку: констрейнт снят, скорость тела сохраняется.
#
# Скриптовый эквивалент того, что делает фильтр:
ps = engine.plugin_manager.get("PhysicsPlugin").physics_scene
origin, direction = viewport.screen_to_ray(x, y)
hit = ps.ray_cast(origin, direction, 1000.0)   # {"entity_id", "body_id", "position"}
cid = ps.create_drag_constraint(hit["body_id"], Vec3(*hit["position"]))
ps.update_drag_constraint(cid, new_target)
ps.remove_drag_constraint(cid)
```

## Проверено по коду

`plugins/physics_plug/plugin.py`, `plugins/physviz/plugin.py`, `plugins/physics_drag_plugin.py`, `plugins/mesh_editor_plugin.py`, `core/components/physics/character/jump_slope.py` (предупреждение про single-mode).
