# Input System

Two layers: low-level `InputManager` (pynput + Qt feed, per-frame pressed/released) and gameplay `Input` facade (`GetKey`, axes, buttons, cursor).

## Properties / API

| Item | Source | Meaning |
|---|---|---|
| `InputManager.start/stop/new_frame` | `core/input/input_manager.py` | Listener lifecycle, per-frame clear |
| `feed_key(vk, pressed) / feed_mouse_button / feed_scroll / set_surface_size` | `input_manager.py` | Qt/event injection |
| `is_key_pressed / key_just_pressed / key_just_released` | `input_manager.py` | `vk` polling |
| `is_mouse_pressed / mouse_just_pressed / mouse_just_released / get_mouse_pos / consume_scroll` | `input_manager.py` | Mouse polling |
| `KeyCode` | `core/input/inpsys/keys.py` | GLFW-like codes: `W` 87, `SPACE` 32, `ESCAPE` 256, `F1`–`F12` 290–301, `MOUSE_LEFT` 1000 …; `from_name(name)` |
| `Input.GetKey/GetKeyDown/GetKeyUp` | `core/input/inpsys/gamepad.py` | Key state by `KeyCode` |
| `Input.GetMouseButton/Down/Up`, `mousePosition/mouseDelta/mouseScrollDelta` | `gamepad.py` | Mouse state |
| `Input.DefineAxis/DefineButton/DefineMouseAxis`, `GetAxis/GetAxisRaw`, `GetButton/Down/Up` | `gamepad.py` | Named mappings (`gravity` 3.0, `dead` 0.001, `sensitivity` 1.0, `snap`, `invert`) |
| `Input.OnEvent/OffEvent`, `anyKey/anyKeyDown` | `gamepad.py` | Event hooks + any-key |
| `cursorLocked/cursorVisible`, `CursorLockMode`, `_Cursor` | `gamepad.py` | Cursor state |

## Usage in Editor

1. Bind project axes/buttons (`Input.DefineAxis("Horizontal", …)`, `DefineButton("Jump", …)`) at startup or via project bindings `LoadProjectBindings`.
2. Read them per frame; call `Input.begin_frame()` / `end_frame()` around updates (handled by the player loop).
3. Lock/hide cursor for FPS mode via `set_cursor_locked` / `set_cursor_visible`.

## Usage in Code

```python
from core.input.inpsys.gamepad import Input
from core.input.inpsys.keys import KeyCode

Input.DefineAxis("Horizontal", positive=[KeyCode.D], negative=[KeyCode.A])
Input.DefineButton("Jump", keys=[KeyCode.SPACE])

def on_update(self, dt):
    x = Input.GetAxis("Horizontal")
    if Input.GetButtonDown("Jump") or Input.GetKeyDown(KeyCode.SPACE):
        self.jump()
```

## Related

- Math (dead zones), GUI events, Player loop

## Verified against

- `core/input/input_manager.py`, `core/input/inpsys/gamepad.py`, `manager.py`, `keys.py`, `constants.py`
