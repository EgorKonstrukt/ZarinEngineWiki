# Урок: FPS-контроллер

Играбельный персонаж от первого лица за 6 шагов: компонент `CharacterController` + связанная камера, без кастомного кода движения. Дефолты ниже — собственные дефолты компонента (`core/components/physics/character/move.py`, `jump_slope.py`).

Ключевые дефолты: `walk_speed = 260.0`, `run_speed = 440.0` (с `Shift`), `crouch_speed = 130.0`, `jump_power = 300.0`, `jump_buffer_time = 0.1`, `coyote_time = 0.1`, `sensitivity = 5.0 / 5.0 / 5.0`, `cursor_lock_on_start = True`, `unlock_key = ESCAPE`, `camera_fov = 60.0`, `gravity = 800.0`, `capsule_radius = 0.5`, `capsule_height = 2.0`, `step_height = 0.3` (высота ступени), `max_slope = 45.0` (лимит склона), `push_strength = 30.0`.

Зашитые клавиши (`get_wish_dir` / `on_fixed_update`): `W/A/S/D` — движение, `Shift` — бег, `Space` — прыжок (с буфером + койот-тайм), `C` — присесть, `правая кнопка` — aim-зум (`aim_fov = 45.0`), колесо — зум при `scroll_zoom`. Обзор мышью внутренний (`sensitivity`, `invert_x/y`, `mouse_smoothing`); готовых осей `Horizontal`/`Vertical` тут не участвует.

Требование: контроллеру нужен внутрипроцессный солвер — поставьте физике `simulation_mode = 'single'` (иначе в логе `physics world is not available in-process. Use single-threaded physics mode`).

## Шаги

1. **Физику проекта в single.** В конфиге проекта `physics.simulation_mode = 'single'` (`PhysicsPlugin._get_physics_settings` читает ключи `physics.*`; кодовый фолбэк — `ensure_single_mode()`).
2. **Создайте игрока.** Hierarchy → добавить сущность, переименовать в `Player`. `Add Component → CharacterController`. Для стандартного гуманоида оставьте `Capsule Radius 0.5 / Height 2.0 / Step Height 0.3 / Max Slope 45`.
3. **Создайте камеру.** Сущность `FPSCamera`, компонент `Camera`, перетащите на `Player` (сделайте ребёнком). Контроллер сам детектит ребёнка (`cam_tr._entity.parent is self._entity`) и ставит его на высоту глаз каждый кадр; либо свяжите свободную камеру полем `Camera` (`camera_entity_id`).
4. **Пол.** Добавьте большую плоскую сущность с коллайдером (статик) на `y = 0`, чтобы `is_character_grounded` было на чём стоять.
5. **Повесьте скрипт-пробу** ниже на `Player` для контрольного вывода.
6. **Нажмите Play.** `WASD` — движение, мышь — обзор (курсор залочен; `Esc` — анлок), `Space` — прыжок, `C` — присесть, `Shift` — спринт. `Walk Speed`, `Jump Power`, `Max Slope`, `Step Height` крутятся живьём в Inspector.

## Полный код — `fps_probe.py`

```python
class FpsProbe:
    def on_awake(self):
        self._cc = self._entity.get_component_by_name("CharacterController")
        self._was_grounded = None

    def on_update(self, dt):
        if self._cc is None:
            return
        grounded = self._cc.is_grounded
        if grounded != self._was_grounded:
            self._was_grounded = grounded
            Logger.info(f"grounded={grounded}")
        if Input.GetKeyDown(KeyCode.SPACE) and not grounded:
            v = self._cc.velocity  # Vec3, ставится солвером каждый fixed update
            Logger.info(f"jump, speed={v.length():.1f}")
```

Заметки: `velocity`, `is_grounded`, `is_crouching`, `fixed_update_count` — readonly-свойства (`move.py`); `get_move_speed()` / `get_wish_dir()` отдают текущее намерение; `set_ai_drive(dir, speed)` ведёт персонажа без клавиш. `resync_character()` пересоздаёт капсулу солвера после смены радиуса/высоты в рантайме.

## Проверено по коду

`core/components/physics/character/move.py`, `core/components/physics/character/jump_slope.py`, `plugins/physics_plug/plugin.py`.
