# Input and Math API

These names are injected into every user script module automatically, so importing them is optional. An explicit import with the same name in the script takes priority over the injection.

Injected names: `Input`, `KeyCode`, `Vec2`, `Vec3`, `Vec4`, `Curve`, `Range`, `Logger`.

Keyboard and mouse:

- `Input.GetKey(code)` — held this frame; `Input.GetKeyDown(code)` — pressed this frame; `Input.GetKeyUp(code)` — released this frame. Codes come from `KeyCode` (`KeyCode.W`, `KeyCode.Space`, `KeyCode.Shift`, arrows, digits and more).
- `Input.GetMouseButton(i)`, `GetMouseButtonDown(i)`, `GetMouseButtonUp(i)` — button index state.
- `Input.GetButton(name)`, `GetButtonDown(name)`, `GetButtonUp(name)` — named action buttons.
- `Input.GetAxis(name)`, `Input.GetAxisRaw(name)` — smoothed vs raw axes such as `"Horizontal"`.
- `Input.mousePosition` — cursor in screen space; `Input.deltaTime` — scaled frame time; `Input.cursorLocked` / `Input.cursorVisible` — pointer capture state.

Math:

- `Vec2`, `Vec3`, `Vec4` — full vector math (add, scale, dot, cross, length, normalized, lerp) backed by `numpy.float64`; the GPU downgrade to float32 happens only on upload.
- `Quat`, `Mat4` — import from `core.maths.math3d` for rotations and matrices (slerp, look_at, perspective helpers live there with numba acceleration).
- `Curve` — keyframed response curves edited in the Inspector and evaluated at runtime for falloff, easing and throttle shapes.
- `Range` — slider metadata for Inspector fields (see Inspector Fields).
- `Logger` — `Logger.info/warning/error` with timestamps; errors include tracebacks and reach the console panel.

Frame-rate independence: always multiply motion by `dt` (or use `Input.deltaTime`); fixed-step logic belongs in `on_fixed_update` with its stable step.

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

Cursor pattern for fly cameras: lock on right-mouse hold, release on up — drive `Input.cursorLocked` from the mouse-button callbacks in `on_update`.
