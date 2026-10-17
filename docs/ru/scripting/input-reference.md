# Справочник ввода

Полный, подтверждённый кодом справочник клавиатуры, мыши, кнопок, осей и курсора. Короткая версия: `input-math-api.md`. Инжектированные имена: `Input`, `KeyCode`, `Cursor` (импорт не нужен); `InputAxis`/`InputButton` — для ручной настройки.

## Опрос

| Сигнатура | Поведение |
|---|---|
| `Input.GetKey(key) -> bool` | Удерживается прямо сейчас. При выключенном вводе — `False`. |
| `Input.GetKeyDown(key) -> bool` | Нажата в этом кадре (фронт из кадрового аккумулятора). |
| `Input.GetKeyUp(key) -> bool` | Отпущена в этом кадре. |
| `Input.GetMouseButton(button) / GetMouseButtonDown / GetMouseButtonUp` | `button` — **индекс**: внутренний код `KeyCode.MOUSE_LEFT + button`, т.е. `0` = левая, `1` = правая, `2` = средняя. |
| `Input.GetButton(name) / GetButtonDown / GetButtonUp` | Именованная `InputButton` (keys + alt keys); неизвестное имя ⇒ `False`. |
| `Input.GetAxis(name) -> float` | Сглаженное значение в [−1, 1]; **неизвестное/неопределённое имя ⇒ `0.0`**. Инвертируется флагом `invert`. |
| `Input.GetAxisRaw(name) -> float` | Несглаженное −1/0/1 (для мыши — дельта); неизвестное ⇒ `0.0`. |
| `Input.mousePosition` / `mouseDelta` / `mouseScrollDelta` | Кортежи `(x, y)`; дельты сбрасываются в конце кадра. |
| `Input.anyKey` / `anyKeyDown` | Что-то удерживается / нажато в этом кадре. |
| `Input.deltaTime` | Кадровый dt с клампом (0…0.1 с). |
| `Input.inputEnabled` + `Input.set_input_enabled(v)` | Мастер-рубильник: при выкл. опрос даёт `False`/`0.0`. |

## Полная таблица KeyCode (точные значения из `keys.py`)

Коды клавиатуры в стиле GLFW; коды мыши начинаются с 1000 (`_is_mouse_key: key >= 1000`).

| Имя | Код | Имя | Код | Имя | Код |
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

Хелпер: `KeyCode.from_name("space")` (регистронезависимый, `0` для неизвестных).

## Оси и кнопки (настройка обязательна)

Неопределённая ось читается как `0.0` — всегда. Определяйте до использования:

| Сигнатура | Поведение |
|---|---|
| `Input.DefineAxis(name, positive, negative, alt_positive=None, alt_negative=None, gravity=3.0, dead=0.001, sensitivity=1.0, snap=False, invert=False)` | Регистрирует сглаженную ось. Значение ползёт к ±1 со скоростью `sensitivity`/с и спадает со скоростью `gravity`/с. |
| `Input.DefineMouseAxis(name)` | Ось дельты мыши (напр. `"Mouse X"`); `GetAxis` вернёт `delta × sensitivity` с опциональной инверсией. |
| `Input.DefineButton(name, keys, alt_keys=None)` | Именованная кнопка поверх списков кодов. |
| `Input.LoadProjectBindings(project_path=None)` | Грузит оси/кнопки/мышь, чувствительность, инверсию и схему из конфига проекта; старые таблицы **очищает**. |
| `Input.set_mouse_sensitivity(v)` / `set_invert_mouse_x/y(v)` / `set_control_scheme("fps"\|"tps")` | Рантайм-настройка. |
| `Input.OnEvent(name, cb)` / `Input.OffEvent(name, cb)` | События кадра/клавиш/мыши/скролла: `any_key_down/up`, `key_down/up`, `mouse_down/up`, `mouse_move`, `scroll`, `frame_begin/end`. |
| `Input.Reset()` | Сброс held/down/up/мыши/скролла. |

## Курсор (только подтверждённое API)

| Член | Поведение |
|---|---|
| `Input.cursorLocked` / `Input.set_cursor_locked(v)` | Флаг захвата указателя (обычный boolean, проброшен в движок). |
| `Input.cursorVisible` / `Input.set_cursor_visible(v)` | Флаг видимости курсора. |
| `Cursor.visible` (get/set) | Алиас `Input.cursorVisible`. |
| `Cursor.lockState` (get/set) | `"locked"` ⇔ (`cursorLocked=True`, `cursorVisible=False`); всё остальное — разблокировать + показать. Константы `CursorLockMode.LOCKED/NONE/CONFINED`. |

Геймпада/джойстика в `core/input/inpsys` нет (только клавиатура + мышь + абстрактные оси/кнопки). Не документируйте вызовы геймпада — они не подтверждены.

## Пример InputManager-обёртки (полный, рабочий)

Одно место, гарантирующее существование всех осей игры, плюс тонкие читатели:

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
        # паттерн fly-камеры (только проверенные флаги):
        if Input.GetMouseButtonDown(1):
            Input.set_cursor_locked(True)
            Input.set_cursor_visible(False)
        if Input.GetMouseButtonUp(1):
            Input.set_cursor_locked(False)
            Input.set_cursor_visible(True)
```

Сторона чтения (любой другой скрипт, настройка не нужна):

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

## Связанные страницы

- `input-math-api.md` — инжектированные имена и векторная математика кратко.
- `transform-scripting.md` — применение прочитанных значений.
- `physics-scripting.md` — опрос в `on_update`, силы в `on_fixed_update`.
- `pitfalls.md` — ловушка `GetAxis` 0.0; индекс против кода в `GetMouseButton`.

## Проверено по коду

- `core/input/inpsys/keys.py:9-146` (вся таблица `KeyCode` + `from_name`), `:148-168` (дефолты `InputAxis`/`InputButton`).
- `core/input/inpsys/gamepad.py:30-61` (опрос клавиш/мыши, `MOUSE_LEFT + button`), `:63-139` (кнопки/оси/define), `:140-199` (свойства), `:201-245` (`LoadProjectBindings`), `:308-344` (события, `Cursor`, `CursorLockMode`).
- `core/input/inpsys/manager.py:41-99` (сглаживание осей, `_is_mouse_key >= 1000`), `:100-136` (press/release/мышь/скролл/reset).
- `core/input/inpsys/__init__.py` (публичная поверхность: API геймпада отсутствует).
