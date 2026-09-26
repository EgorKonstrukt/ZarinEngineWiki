# API ввода и математики

Эти имена подставляются в модуль каждого пользовательского скрипта автоматически, импортировать их не обязательно. Явный импорт с тем же именем в скрипте приоритетнее подстановки.

Подставляемые имена: `Input`, `KeyCode`, `Vec2`, `Vec3`, `Vec4`, `Curve`, `Range`, `Logger`.

Клавиатура и мышь:

- `Input.GetKey(code)` — зажата в этом кадре; `Input.GetKeyDown(code)` — нажата в этом кадре; `Input.GetKeyUp(code)` — отпущена. Коды из `KeyCode` (`KeyCode.W`, `KeyCode.Space`, `KeyCode.Shift`, стрелки, цифры и другие).
- `Input.GetMouseButton(i)`, `GetMouseButtonDown(i)`, `GetMouseButtonUp(i)` — состояние кнопок по индексу.
- `Input.GetButton(name)`, `GetButtonDown(name)`, `GetButtonUp(name)` — именованные кнопки действий.
- `Input.GetAxis(name)`, `Input.GetAxisRaw(name)` — сглаженные против сырых осей вроде `"Horizontal"`.
- `Input.mousePosition` — курсор в экранных координатах; `Input.deltaTime` — масштабированное время кадра; `Input.cursorLocked` / `Input.cursorVisible` — состояние захвата указателя.

Математика:

- `Vec2`, `Vec3`, `Vec4` — полная векторная математика (сложение, масштаб, dot, cross, length, normalized, lerp) на `numpy.float64`; понижение до float32 для GPU только при загрузке.
- `Quat`, `Mat4` — импортируются из `core.maths.math3d` для поворотов и матриц (slerp, look_at, perspective-хелперы там же с numba-ускорением).
- `Curve` — кейфреймовые кривые отклика из инспектора с вычислением в рантайме для спадов, изингов и форм газа.
- `Range` — метаданные слайдеров полей инспектора (см. поля инспектора).
- `Logger` — `Logger.info/warning/error` с таймстампами; ошибки с трейсбеками доходят до панели консоли.

Независимость от FPS: всегда умножайте движение на `dt` (или берите `Input.deltaTime`); логика фиксированного шага — в `on_fixed_update` со стабильным шагом.

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

```python
class Looker:
    sensitivity: float = 2.0

    def on_update(self, dt):
        x = Input.GetAxis("Horizontal")
        t = self._entity.transform
        if t and x != 0.0:
            t.rotate(Vec3(0.0, x * self.sensitivity * dt, 0.0))
```

Паттерн курсора для fly-камер: лок при зажатой правой кнопке, релиз по отпусканию — ведите `Input.cursorLocked` из колбэков кнопок мыши в `on_update`.
