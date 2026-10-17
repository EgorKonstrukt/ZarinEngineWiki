# Input Reference

Complete, code-backed reference for keyboard, mouse, buttons, axes, and cursor. Shorter narrative version: `input-math-api.md`. Injected names: `Input`, `KeyCode`, `Cursor` (no import needed); `InputAxis`/`InputButton` for manual setup.

## Polling

| Signature | Behavior |
|---|---|
| `Input.GetKey(key) -> bool` | Held right now. Returns `False` when input is disabled. |
| `Input.GetKeyDown(key) -> bool` | Pressed this frame (edge from the frame accumulator). |
| `Input.GetKeyUp(key) -> bool` | Released this frame. |
| `Input.GetMouseButton(button) / GetMouseButtonDown / GetMouseButtonUp` | `button` is an **index**: internal code is `KeyCode.MOUSE_LEFT + button`, so `0` = left, `1` = right, `2` = middle. |
| `Input.GetButton(name) / GetButtonDown / GetButtonUp` | Named `InputButton` (keys + alt keys); unknown name ⇒ `False`. |
| `Input.GetAxis(name) -> float` | Smoothed value in [−1, 1]; **unknown or undefined name ⇒ `0.0`**. Negated when the axis is `invert`ed. |
| `Input.GetAxisRaw(name) -> float` | Unsmoothed −1/0/1 (or mouse delta for mouse axes); unknown ⇒ `0.0`. |
| `Input.mousePosition` / `mouseDelta` / `mouseScrollDelta` | `(x, y)` tuples; deltas reset at frame end. |
| `Input.anyKey` / `anyKeyDown` | Held-anything / pressed-this-frame. |
| `Input.deltaTime` | Clamped frame dt (0…0.1 s). |
| `Input.inputEnabled` + `Input.set_input_enabled(v)` | Master gate: polling returns `False`/`0.0` while off. |

## Full KeyCode table (exact values from `keys.py`)

Keyboard codes are GLFW-style; mouse codes start at 1000 (`_is_mouse_key: key >= 1000`).

| Name | Code | Name | Code | Name | Code |
|---|---|---|---|---|---|
| `SPACE` | 32 | `A` | 65 | `N` | 78 |
| `APOSTROPHE` | 39 | `B` | 66 | `O` | 79 |
| `COMMA` | 44 | `C` | 67 | `P` | 80 |
| `MINUS` | 45 | `D` | 68 | `Q` | 81 |
| `PERIOD` | 46 | `E` | 69 | `R` | 82 |
| `SLASH` | 47 | `F` | 70 | `S` | 83 |
| `KEY_0` | 48 | `G` | 71 | `T` | 84 |
| `KEY_1` | 49 | `H` | 72 | `U` | 85 |
| `KEY_2` | 50 | `I` | 73 | `V` | 86 |
| `KEY_3` | 51 | `J` | 74 | `W` | 87 |
| `KEY_4` | 52 | `K` | 75 | `X` | 88 |
| `KEY_5` | 53 | `L` | 76 | `Y` | 89 |
| `KEY_6` | 54 | `M` | 77 | `Z` | 90 |
| `KEY_7` | 55 | `LEFT_BRACKET` | 91 | `BACKSLASH` | 92 |
| `KEY_8` | 56 | `RIGHT_BRACKET` | 93 | `GRAVE_ACCENT` | 96 |
| `KEY_9` | 57 | `WORLD_1` | 161 | `WORLD_2` | 162 |
| `SEMICOLON` | 59 | `ESCAPE` | 256 | `CAPS_LOCK` | 280 |
| `EQUAL` | 61 | `ENTER` | 257 | `SCROLL_LOCK` | 281 |
| `TAB` | 258 | `NUM_LOCK` | 282 | `BACKSPACE` | 259 |
| `INSERT` | 260 | `PRINT_SCREEN` | 283 | `DELETE` | 261 |
| `PAUSE` | 284 | `RIGHT` | 262 | `LEFT` | 263 |
| `DOWN` | 264 | `UP` | 265 | `PAGE_UP` | 266 |
| `PAGE_DOWN` | 267 | `HOME` | 268 | `END` | 269 |
| `F1` | 290 | `F2` | 291 | `F3` | 292 |
| `F4` | 293 | `F5` | 294 | `F6` | 295 |
| `F7` | 296 | `F8` | 297 | `F9` | 298 |
| `F10` | 299 | `F11` | 300 | `F12` | 301 |
| `F13` | 302 | `F14` | 303 | `F15` | 304 |
| `F16` | 305 | `F17` | 306 | `F18` | 307 |
| `F19` | 308 | `F20` | 309 | `F21` | 310 |
| `F22` | 311 | `F23` | 312 | `F24` | 313 |
| `F25` | 314 | `KP_0` | 320 | `KP_1` | 321 |
| `KP_2` | 322 | `KP_3` | 323 | `KP_4` | 324 |
| `KP_5` | 325 | `KP_6` | 326 | `KP_7` | 327 |
| `KP_8` | 328 | `KP_9` | 329 | `KP_DECIMAL` | 330 |
| `KP_DIVIDE` | 331 | `KP_MULTIPLY` | 332 | `KP_SUBTRACT` | 333 |
| `KP_ADD` | 334 | `KP_ENTER` | 335 | `KP_EQUAL` | 336 |
| `LEFT_SHIFT` | 340 | `LEFT_CONTROL` | 341 | `LEFT_ALT` | 342 |
| `LEFT_SUPER` | 343 | `RIGHT_SHIFT` | 344 | `RIGHT_CONTROL` | 345 |
| `RIGHT_ALT` | 346 | `RIGHT_SUPER` | 347 | `MENU` | 348 |
| `MOUSE_LEFT` | 1000 | `MOUSE_RIGHT` | 1001 | `MOUSE_MIDDLE` | 1002 |
| `MOUSE_BACK` | 1003 | `MOUSE_FORWARD` | 1004 | — | — |

Helpers: `KeyCode.from_name("space")` (case-insensitive, `0` when unknown).

## Axes and buttons (setup is mandatory)

An axis that was never defined reads as `0.0` — always. Define before use:

| Signature | Behavior |
|---|---|
| `Input.DefineAxis(name, positive, negative, alt_positive=None, alt_negative=None, gravity=3.0, dead=0.001, sensitivity=1.0, snap=False, invert=False)` | Registers a smoothed axis. Value ramps toward ±1 at `sensitivity`/s and falls back at `gravity`/s. |
| `Input.DefineMouseAxis(name)` | Registers a mouse-delta axis (e.g. `"Mouse X"`); `GetAxis` returns `delta × sensitivity` with optional inversion. |
| `Input.DefineButton(name, keys, alt_keys=None)` | Registers a named button over key-code lists. |
| `Input.LoadProjectBindings(project_path=None)` | Loads axes/buttons/mouse axes, sensitivity, inversion, and control scheme from the project config; **clears** existing maps first. |
| `Input.set_mouse_sensitivity(v)` / `set_invert_mouse_x/y(v)` / `set_control_scheme("fps"\|"tps")` | Runtime tuning. |
| `Input.OnEvent(name, cb)` / `Input.OffEvent(name, cb)` | Frame/key/mouse/scroll events: `any_key_down/up`, `key_down/up`, `mouse_down/up`, `mouse_move`, `scroll`, `frame_begin/end`. |
| `Input.Reset()` | Clears held/down/up/mouse/scroll state. |

## Cursor (confirmed API only)

| Member | Behavior |
|---|---|
| `Input.cursorLocked` / `Input.set_cursor_locked(v)` | Pointer-capture flag (plain boolean, engine-bridged). |
| `Input.cursorVisible` / `Input.set_cursor_visible(v)` | Cursor visibility flag. |
| `Cursor.visible` (get/set) | Alias of `Input.cursorVisible`. |
| `Cursor.lockState` (get/set) | `"locked"` ⇔ (`cursorLocked=True`, `cursorVisible=False`); anything else unlocks + shows. `CursorLockMode.LOCKED/NONE/CONFINED` constants. |

No gamepad/joystick polling exists in `core/input/inpsys` (keyboard + mouse + abstract axes/buttons only). Anything beyond the table above is not confirmed — do not document gamepad calls.

## InputManager wrapper example (full, runnable)

One place that guarantees every axis the game uses exists, then thin readers:

```python
class InputSetup:
    def on_awake(self):
        Input.DefineAxis("Horizontal", [KeyCode.D], [KeyCode.A])
        Input.DefineAxis("Vertical", [KeyCode.W], [KeyCode.S])
        Input.DefineAxis("Strafe", [KeyCode.E], [KeyCode.Q], sensitivity=2.0)
        Input.DefineButton("Jump", [KeyCode.SPACE])
        Input.DefineButton("Fire", [KeyCode.MOUSE_LEFT])
        Input.DefineMouseAxis("Mouse X")
        Input.DefineMouseAxis("Mouse Y")

    def on_update(self, dt):
        # fly-camera cursor pattern (verified flags only):
        if Input.GetMouseButtonDown(1):
            Input.set_cursor_locked(True)
            Input.set_cursor_visible(False)
        if Input.GetMouseButtonUp(1):
            Input.set_cursor_locked(False)
            Input.set_cursor_visible(True)
```

Reader side (any other script, no setup needed):

```python
class ShooterInput:
    def on_update(self, dt):
        if Input.GetButtonDown("Jump"):
            Logger.info("jump!")
        if Input.GetMouseButtonDown(0):   # == MOUSE_LEFT
            Logger.info(f"fire at {Input.mousePosition}")
        yaw = Input.GetAxis("Mouse X")
        if yaw != 0.0:
            self._entity.transform.rotate(Vec3(0.0, yaw * 120.0 * dt, 0.0))
```

## Related

- `input-math-api.md` — injected names and vector math in brief.
- `transform-scripting.md` — applying the values read here.
- `physics-scripting.md` — polling in `on_update`, forces in `on_fixed_update`.
- `pitfalls.md` — `GetAxis` 0.0 trap; `GetMouseButton` index vs code.

## Verified against

- `core/input/inpsys/keys.py:9-146` (entire `KeyCode` table + `from_name`), `:148-168` (`InputAxis`/`InputButton` defaults).
- `core/input/inpsys/gamepad.py:30-61` (key/mouse polling, `MOUSE_LEFT + button`), `:63-139` (buttons/axes/define), `:140-199` (properties), `:201-245` (`LoadProjectBindings`), `:308-344` (events, `Cursor`, `CursorLockMode`).
- `core/input/inpsys/manager.py:41-99` (axis smoothing, `_is_mouse_key >= 1000`), `:100-136` (press/release/mouse/scroll/reset).
- `core/input/inpsys/__init__.py` (public surface: no gamepad API).
