# Контроллер персонажа

Кинематическое тело first/third-person на move-функции солвера — скольжение по склонам, подъём по ступеням, толкаемый реквизит без хаоса rigidbody.

## Свойства

| Поле | По умолчанию | Примечания |
|---|---|---|
| `walk_speed` / `run_speed` (Shift) / `crouch_speed` | `260.0` / `440.0` / `130.0` | Ед/с |
| `acceleration` / `air_acceleration` | `10.0` / `10.0` | |
| `friction` / `stop_speed` | `4.0` / `80.0` | Модель трения |
| `jump_power` | `300.0` | Буфер прыжка / койот `0.1` с |
| `crouch_toggle` / `crouch_speed_mult` / `crouch_eye_offset` | `True` / `0.5` / `0.6` | |
| `sensitivity` / `sensitivity_x/y` | `5.0` / `5.0` / `5.0` | `mouse_smoothing` `0.0`, `smoothing_strength` `18.0` |
| `cursor_lock_on_start` / `unlock_key` | `True` / `ESCAPE` | |
| `camera_fov` / `near` / `far` | `60.0` / `0.05` / `1000.0` | Зум `20–90`, прицел `45` скорость `12` |
| `gravity` | `800.0` | Локальная гравитация контроллера, не мировая |
| `capsule_radius` / `capsule_height` / `crouch_height` | `0.5` / `2.0` / `1.2` | Итоговая высота `≥ radius*2 + 0.1` |
| `step_height` / `max_slope` / `push_strength` | `0.3` / `45.0` / `30.0` | Уходят в `solver.create_character` |
| Только чтение | `velocity`, `is_grounded`, `is_crouching`, `fixed_update_count` | `set_ai_drive(dir, speed)` для ИИ |

## Использование в редакторе

Добавить `CharacterController` (без `Rigidbody`), привязать `Camera`, настроить скорости/гравитацию/капсулу. WASD + мышь, Shift — бег.

## Использование в коде

```python
entity = scene.create_entity("Player")
from core.components.physics.character.jump_slope import CharacterController

cc = CharacterController()
cc.walk_speed = 260.0
cc.jump_power = 300.0
cc.capsule_radius = 0.5
cc.capsule_height = 2.0
entity.add_component(cc)

# Привод от ИИ вместо клавиатуры:
# cc.set_ai_drive(Vec3(0.0, 0.0, 1.0), 200.0)
```

## Заметки о fixed update

- Движение — в `on_fixed_update(dt)` при `dt = Engine._fixed_dt` (`0.02`); осмотр мышью — в `on_update`.
- Требует режим `single`: `on_start` резолвит солвер через `_resolve_solver()` с фолбэком `plugin.ensure_single_mode()`; без `culverin` персонаж не создаётся.
- Хэндл: `solver.create_character(pos, height, radius, step_height, max_slope)` + `move_character` / `is_character_grounded` / `destroy_character` (в `on_disable` уничтожается).

## Связанные

Rigidbody, Capsule, Joints, солвер и потоки (требование режима), слои.

## Проверено по

- `core/components/physics/character/move.py`, `character/jump_slope.py`
- `core/physics/physics_solver.py`, `plugins/physics_plug/plugin.py`
