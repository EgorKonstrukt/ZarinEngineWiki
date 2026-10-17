# Физика из кода

Приложение сил и чтение скоростей из игрового кода, плюс точный контракт колбэков столкновений.

## Rigidbody (3D)

| Член | Поведение |
|---|---|
| `rb.velocity: Vec3` | Чтение/запись; запись ставит `_velocity_dirty`, чтобы солвер подхватил. |
| `rb.angular_velocity: Vec3` | Тот же механизм dirty-флага. |
| `rb.add_force(force: Vec3, world_space=True)` | Копит в `_force_accum`, разбирается шагом солвера. |
| `rb.add_torque(torque: Vec3)` | Копит в `_torque_accum`. |
| `rb.add_impulse(impulse: Vec3)` | Мгновенно `velocity += impulse / mass`; игнорируется при `mass <= 0` или `is_kinematic`. |
| `rb.mass` / `rb.drag` / `rb.angular_drag` | Дефолты `1.0` / `0.0` / `0.05`. |
| `rb.use_gravity` / `rb.is_kinematic` | Тумблер гравитации; кинематика игнорирует импульсы. |
| `rb.freeze_pos_x/y/z`, `rb.freeze_rot_x/y/z` | Блокировки по осям (сериализуются массивами `freeze_pos`/`freeze_rot`). |

## Rigidbody2D

| Член | Поведение |
|---|---|
| `rb.velocity: Vec2` / `rb.angular_velocity: float` | Тот же путь записи с dirty-флагом. |
| `rb.add_force(force: Vec2)` | 2D-аккумулятор сил. |
| `rb.add_torque(torque: float)` | Скалярный аккумулятор момента. |
| `rb.add_impulse(impulse: Vec2)` | Мгновенный пинок скорости, тот же гард массы/кинематики. |
| `rb.gravity_scale` (по умолчанию `1.0`) | Только 2D — множитель гравитации. |
| `rb.freeze_rotation: bool` | 2D-блокировка поворота. |

## Колбэки столкновений (точный контракт)

Допустима любая арность; движок меряет число параметров **один раз** при загрузке (`2 if nargs >= 2 else 1`) и диспетчит соответственно:

```python
def on_collision_enter(self, other_id): ...          # арность 1
def on_collision_enter(self, other_id, force): ...   # арность 2
def on_collision_stay(self, other_id): ...           # или (self, other_id, force)
def on_collision_exit(self, other_id): ...           # или (self, other_id, force)
```

`other_id` — **строковый id сущности** другого тела; `force` — float силы контакта солвера (`ev.get("force", 0.0)`). Колбэки приходят из слоя событий физики, а не из ECS-цикла, и каждый контакт стреляет в скриптах **обеих** сущностей. Резолвите сущность только при нужде — сравнение id дешевле:

```python
def on_collision_enter(self, other_id, force):
    if force > 50.0:
        Logger.warning(f"Hard hit by {other_id}: {force}")
```

## Пример прыжка и отдачи (полный, рабочий)

Силы — в `on_fixed_update` (стабильный шаг), опрос ввода — в `on_update`:

```python
class Jumper:
    jump_impulse: float = 8.0
    move_force: float = 30.0

    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody")
        self.want_jump = False

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            self.want_jump = True

    def on_fixed_update(self, dt):
        if self.body is None:
            return
        fx = Input.GetAxis("Horizontal") * float(self.move_force)
        if fx != 0.0:
            self.body.add_force(Vec3(fx, 0.0, 0.0))
        if self.want_jump:
            self.want_jump = False
            v = self.body.velocity
            self.body.velocity = Vec3(v.x, 0.0, v.z)  # сброс скорости падения
            self.body.add_impulse(Vec3(0.0, float(self.jump_impulse), 0.0))

    def on_collision_enter(self, other_id, force):
        if force > 40.0:
            # отдача: пинок от удара, масса учтена бесплатно
            v = self.body.velocity if self.body is not None else Vec3.zero()
            if self.body is not None:
                self.body.velocity = Vec3(-v.x * 0.5, v.y, -v.z * 0.5)
```

Поля инспектора: `jump_impulse`, `move_force` (float). Нужен `Rigidbody` на той же сущности и определённая ось `"Horizontal"` (см. `input-reference.md`; неопределена ⇒ `0.0`, тело просто не будет стрейфиться).

2D-вариант (прыжок с учётом гравитации):

```python
class Hopper2D:
    jump_impulse: float = 6.0

    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody2D")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE) and self.body is not None:
            self.body.velocity = Vec2(self.body.velocity.x, 0.0)
            self.body.add_impulse(Vec2(0.0, float(self.jump_impulse)))
```

## Связанные страницы

- `lifecycle.md` — тайминг `on_fixed_update` и строки коллизий.
- `transform-scripting.md` — кинематику/нефизические объекты двигайте через `Transform`.
- `input-reference.md` — `DefineAxis` (обязателен, иначе `GetAxis` бесполезен).
- `pitfalls.md` — силы принадлежат `on_fixed_update`, а не `on_update`.

## Проверено по коду

- `core/components/physics/rigidbody.py:29-75` (поля, dirty-флаг скорости, `add_force`/`add_torque`/`add_impulse` + гарды), `:76-98` (сериализация freeze).
- `core/components/physics2d/rigidbody2d.py:30-81` (`gravity_scale`, скалярный момент, `Vec2`-API).
- `core/components/scripting/scriptc/hotreload.py:240-248` (детект арности `2 if nargs >= 2 else 1`).
- `core/physics/scene/events.py:294-336` (извлечение force, двусторонний диспетч, ветка арности).
- `tests/smoke/test_scripting.py:228-255` (ожидания колбэков с 1 и 2 аргументами).
