# API ввода и математики

Эти имена подставляются в модуль каждого пользовательского скрипта автоматически, импортировать их не обязательно. Явный импорт с тем же именем в скрипте имеет приоритет.

Подставляемые имена: `Input`, `KeyCode`, `Vec2`, `Vec3`, `Vec4`, `Curve`, `Range`, `Logger`.

```python
class Player:
    speed: float = 6.0

    def on_update(self, dt):
        step = self.speed * dt
        t = self._entity.transform
        if Input.GetKey(KeyCode.W):
            t.translate(Vec3(0.0, 0.0, step))
        if Input.GetKey(KeyCode.S):
            t.translate(Vec3(0.0, 0.0, -step))
        if Input.GetKeyDown(KeyCode.Space):
            Logger.info("jump pressed")
```

Доступный API ввода:

- `Input.GetKey`, `Input.GetKeyDown`, `Input.GetKeyUp` — состояние клавиатуры по `KeyCode`.
- `Input.GetMouseButton`, `GetMouseButtonDown`, `GetMouseButtonUp` — кнопки мыши.
- `Input.GetButton`, `GetButtonDown`, `GetButtonUp` — именованные кнопки.
- `Input.GetAxis`, `Input.GetAxisRaw` — именованные оси, например `"Horizontal"`.
- `Input.mousePosition`, `Input.deltaTime`, `Input.cursorLocked`, `Input.cursorVisible`.

Доступная математика:

- `Vec2`, `Vec3`, `Vec4` — векторы на `numpy.float64`.
- `Quat`, `Mat4` — при необходимости импортируются из `core.maths.math3d`.
- `Curve` — кейфреймовая кривая с виджетом редактора и вычислением в рантайме.

```python
class Looker:
    sensitivity: float = 2.0

    def on_update(self, dt):
        x = Input.GetAxis("Horizontal")
        t = self._entity.transform
        if t and x != 0.0:
            t.rotate(Vec3(0.0, x * self.sensitivity * dt, 0.0))
```
