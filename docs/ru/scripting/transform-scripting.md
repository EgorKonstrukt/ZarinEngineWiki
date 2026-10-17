# Трансформ из скрипта

Движение, повороты и наведение сущностей из игрового кода. Путь импорта: `from core.components.transform.tr import Transform`. Векторы: инжектированный `Vec3` (импорт внутри скриптов поведения не нужен).

## Доступ к полям

| Член | Тип / единицы | Замечания |
|---|---|---|
| `transform.local_position` | `Vec3`, мировые единицы | Позиция относительно родителя. Присваивайте `Vec3` (или список/кортеж/ndarray). |
| `transform.local_rotation` | `Quat` | Локальный кватернион поворота. |
| `transform.local_euler_angles` | `Vec3`, **градусы** | Удобный поворот; конвертация через `Quat.from_euler`/`to_euler`. |
| `transform.local_scale` | `Vec3` | Локальный масштаб, по умолчанию `(1, 1, 1)`. |
| `transform.position` | `Vec3`, мировые единицы | Мировая позиция; сеттер пересчитывает локальную через инверсию родителя. |
| `transform.world_matrix` | `Mat4` | Полная мировая матрица; установка идёт через отложенный world-target путь. |
| `transform.forward` | `Vec3` (нормированный) | **−Z** мировой матрицы. |
| `transform.right` | `Vec3` (нормированный) | **+X** мировой матрицы. |
| `transform.up` | `Vec3` (нормированный) | **+Y** мировой матрицы. |
| `transform.set_local_position_xyz(x, y, z)` | — | Запись по месту, без временного `Vec3`. |
| `transform.set_local_scale_xyz(x, y, z)` | — | Запись по месту. |

## Методы движения

| Сигнатура | Поведение |
|---|---|
| `transform.translate(delta: Vec3, world_space=False)` | `False` (по умолчанию): прибавляет к **локальной** позиции. `True`: к **мировой** (с учётом родителя). |
| `transform.rotate(euler: Vec3)` | Домножает локальный кватернион на кватернион из эйлеровых углов **в градусах** (перевод в радианы внутри). |
| `transform.look_at(target: Vec3, up=None)` | Разворачивает forward (`-Z`) на `target` через `(target − position).normalized()` + `Quat.look_rotation`; up по умолчанию `Vec3.up()`. |

Каждая мутация помечает трансформ dirty и ставит `_physics_dirty` — коллайдеры и матрицы рендера подтянутся при следующем flush.

## Пример преследователя (полный, рабочий)

```python
class Follower:
    damping: float = 5.0
    height: float = 2.0

    def on_start(self):
        self.target = self._entity._scene.get_entity_by_name("Player")

    def on_update(self, dt):
        if self.target is None:
            return
        t = self._entity.transform
        goal = self.target.transform.position + Vec3(0.0, self.height, 0.0)
        cur = t.position
        k = min(1.0, float(self.damping) * float(dt))
        t.position = cur.lerp(goal, k)
        t.look_at(self.target.transform.position)
```

Поля инспектора: `damping` (float), `height` (float). Цель ищется по имени, чтобы не тянуть зависимости; поле типа `Entity` тоже работает (см. `inspector-fields.md`).

## Пример орбитальной камеры (полный, рабочий)

Демонстрирует `rotate` в **градусах**, `translate` в мировом пространстве и forward −Z:

```python
class OrbitCamera:
    radius: float = 8.0
    angular_speed: float = 30.0  # градусов в секунду
    height: float = 3.0

    def on_start(self):
        self.angle = 0.0
        self.pivot = self._entity._scene.get_entity_by_name("Player")
        self.t = self._entity.transform

    def on_update(self, dt):
        self.angle += float(self.angular_speed) * float(dt)
        import math
        a = math.radians(self.angle)
        cx, cz = 0.0, 0.0
        if self.pivot is not None:
            p = self.pivot.transform.position
            cx, cz = p.x, p.z
        self.t.position = Vec3(
            cx + self.radius * math.cos(a),
            self.height,
            cz + self.radius * math.sin(a),
        )
        look = self.pivot.transform.position if self.pivot is not None else Vec3(0.0, 0.0, 0.0)
        self.t.look_at(look)
```

Вариант рысканья с клавиатуры (единицы `rotate` явно):

```python
class YawControl:
    turn_speed: float = 90.0  # градусов в секунду

    def on_update(self, dt):
        t = self._entity.transform
        if Input.GetKey(KeyCode.LEFT):
            t.rotate(Vec3(0.0, self.turn_speed * dt, 0.0))   # градусы!
        if Input.GetKey(KeyCode.RIGHT):
            t.rotate(Vec3(0.0, -self.turn_speed * dt, 0.0))
        if Input.GetKey(KeyCode.W):
            t.translate(t.forward * 5.0 * dt, world_space=True)
```

## Связанные страницы

- `entity-api.md` — `entity.transform` бывает `None` у голых сущностей.
- `input-reference.md` — используемые выше коды клавиш.
- `physics-scripting.md` — кинематику двигайте скоростью, динамику — силами.
- `pitfalls.md` — у `Vec3` нет `.rotate`; углы — градусы, не радианы.

## Проверено по коду

- `core/components/transform/tr/component.py:47-118` (локальные акцессоры, `local_euler_angles` в градусах).
- `core/components/transform/tr/matrix.py:227-279` (`position`, `world_matrix`), `:281-318` (`forward −Z`, `right`, `up`), `:319-354` (`translate`), `:355-384` (`rotate`, `math.radians` внутри), `:385-390` (`look_at`).
- `core/components/transform/tr/__init__.py` (класс `Transform`, путь импорта).
