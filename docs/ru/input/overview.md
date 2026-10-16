# Система ввода

Два слоя: низкий `InputManager` (pynput + Qt feed, pressed/released за кадр) и игровой фасад `Input`.

## Свойства / API

| Элемент | Источник | Смысл |
|---|---|---|
| `InputManager.start/stop/new_frame` | `core/input/input_manager.py` | Жизнь слушателей, очистка за кадр |
| `feed_key / feed_mouse_button / feed_scroll` | `input_manager.py` | Инжекция из Qt/событий |
| `is_key_pressed / key_just_pressed / key_just_released` | `input_manager.py` | Опрос по `vk` |
| `KeyCode` | `inpsys/keys.py` | Коды в стиле GLFW: `W` 87, `SPACE` 32, `ESCAPE` 256, `F1`–`F12` 290–301, `MOUSE_LEFT` 1000 …; `from_name` |
| `Input.GetKey/GetKeyDown/GetKeyUp` | `inpsys/gamepad.py` | Состояние клавиш |
| `Input.DefineAxis/DefineButton`, `GetAxis/GetButton` | `gamepad.py` | Именованные маппинги (`gravity` 3.0, `dead` 0.001) |
| `Input.OnEvent/OffEvent`, курсор | `gamepad.py` | Хуки, `cursorLocked/cursorVisible` |

## Использование в редакторе

1. Забиндите оси/кнопки (`DefineAxis("Horizontal", …)`) на старте или через `LoadProjectBindings`.
2. Читайте каждый кадр; `begin_frame`/`end_frame` крутит плеер.
3. Для FPS лочите/прячьте курсор.

## Использование в коде

```python
from core.input.inpsys.gamepad import Input
from core.input.inpsys.keys import KeyCode

Input.DefineAxis("Horizontal", positive=[KeyCode.D], negative=[KeyCode.A])
Input.DefineButton("Jump", keys=[KeyCode.SPACE])

def on_update(self, dt):
    x = Input.GetAxis("Horizontal")
    if Input.GetButtonDown("Jump"):
        self.jump()
```

## Связанные

- GUI events, цикл плеера

## Проверено по

- `core/input/input_manager.py`, `core/input/inpsys/gamepad.py`, `keys.py`
