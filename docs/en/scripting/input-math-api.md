# Input and Math API

These names are injected into every user script module automatically, so importing them is optional. An explicit import with the same name in the script takes priority.

Injected names: `Input`, `KeyCode`, `Vec2`, `Vec3`, `Vec4`, `Curve`, `Range`, `Logger`.

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

Available input API:

- `Input.GetKey`, `Input.GetKeyDown`, `Input.GetKeyUp` — keyboard state by `KeyCode`.
- `Input.GetMouseButton`, `GetMouseButtonDown`, `GetMouseButtonUp` — mouse buttons.
- `Input.GetButton`, `GetButtonDown`, `GetButtonUp` — named buttons.
- `Input.GetAxis`, `Input.GetAxisRaw` — named axes such as `"Horizontal"`.
- `Input.mousePosition`, `Input.deltaTime`, `Input.cursorLocked`, `Input.cursorVisible`.

Available math:

- `Vec2`, `Vec3`, `Vec4` — vectors backed by `numpy.float64`.
- `Quat`, `Mat4` — import from `core.maths.math3d` when needed.
- `Curve` — keyframed curve with editor widget and runtime evaluation.

```python
class Looker:
    sensitivity: float = 2.0

    def on_update(self, dt):
        x = Input.GetAxis("Horizontal")
        t = self._entity.transform
        if t and x != 0.0:
            t.rotate(Vec3(0.0, x * self.sensitivity * dt, 0.0))
```
