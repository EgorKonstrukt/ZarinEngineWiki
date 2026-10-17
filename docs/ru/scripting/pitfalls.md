# Грабли скриптинга

Симптом → причина → фикс. Каждый пункт опирается на реализацию из раздела проверок.

## 1. `Vec3(...).rotate(...)` → `AttributeError`

Симптом: поворот вектора падает. Причина: у `Vec3` есть `cross/dot/length/normalized/lerp/...`, но **нет `rotate`**. Фикс: вращайте трансформ или вектор через кватернион:

```python
# неверно: v.rotate(...)
# верно (объект поворачивается, градусы):
self._entity.transform.rotate(Vec3(0.0, 90.0 * dt, 0.0))
# верно (вектор поворачивается, радианы через axis-angle):
from core.maths.math3d import Quat
q = Quat.from_axis_angle(Vec3.up(), 1.5708)
v2 = q.rotate_vec3(v)
```

## 2. Выполняется не тот класс (или ничего)

Симптом: движок инстанцирует неожиданный класс из файла. Причина: обнаружение сканирует `dir(module)` — **по алфавиту, не по порядку определения** — и берёт первый класс с любым lifecycle/collision/gizmo-членом или `_inspector_buttons`. Фикс: один класс поведения на файл, либо имя поведения первым по алфавиту; у хелпер-классов не должно быть `on_update`/`start`/т.п.

## 3. `GetAxis("Horizontal")` всегда `0.0`

Симптом: оси мертвы, клавиши работают. Причина: неопределённые оси по контракту читаются как `0.0`. Фикс: определите до использования (или грузите биндинги проекта):

```python
Input.DefineAxis("Horizontal", [KeyCode.D], [KeyCode.A])
Input.DefineAxis("Vertical", [KeyCode.W], [KeyCode.S])
```

## 4. Звук стартует при выключенном `play_on_awake`

Симптом: активация сущности запускает клип. Причина: `AudioSource.on_enable` играет, когда задан `clip_path` и тихо — независимо от `play_on_awake`. Фикс: держите `clip_path` пустым, пока не нужно, или делайте `stop()` в `on_enable` у менеджеров эмбиента:

```python
def on_enable(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src is not None and not self.want_sound:
        src.stop()
```

## 5. Курсор не локается / ось мыши мертва

Симптом: `cursorLocked` выставлен, но ничего не меняется, или `GetAxis("Mouse X")` — `0.0`. Причина: флаги — обычные проброшенные boolean (ставьте и лок, **и** видимость), а осям мыши нужен `DefineMouseAxis`. Фикс:

```python
Input.set_cursor_locked(True)
Input.set_cursor_visible(False)
Input.DefineMouseAxis("Mouse X")
```

API геймпада в `core/input/inpsys` нет — только клавиатура, мышь и абстрактные оси/кнопки.

## 6. Cython-импорт падает / код stale

Симптом: `import fast_math` (соседний `fast_math.pyx`) падает или игнорирует правки. Причина: соседний модуль собирается **синхронно при первом импорте** через in-process pyximport-хук; отсутствующий пакет `cython` даёт лишь одно предупреждение, а импорт затем падает. Правки подхватываются, потому что релоадер снапшотит mtime расширений и пересобирает при изменении. Фикс: держите `.pyx` рядом с `.py`, поставьте `cython`, больше ничего не трогайте:

```python
# myscript.py + fast_math.pyx в ОДНОЙ папке:
import fast_math  # резолвится из папки скрипта автоматически
```

## 7. Заспавненная сущность невидима / без позиции

Симптом: объект `create_entity` нельзя разместить. Причина: новые сущности **голые — без `Transform`**. Фикс: всегда добавляйте (обращение к `transform` у `None` упадёт):

```python
from core.components.transform.tr import Transform
e = scene.create_entity("Prop")
e.add_component(Transform())   # до касаний e.transform
```

## 8. Сюрпризы порядка `on_fixed_update`

Симптом: два скрипта гонятся (писатель против читателя). Причина: апдейты идут по кэшированным спискам порядка сцены; межсущностный порядок — деталь реализации, не контракт. Фикс: не полагайтесь на порядок соседей — пишите в `on_fixed_update`, читайте с лагом в кадр или резолвите лениво, никогда не предполагайте, кто первый.

## 9. Правки инспектора пропадают / типы не те

Симптом: поле видно, но значение сбрасывается, или `Vec3` приезжает списком. Причина: два хранилища — `_field_values` (источник истины, на диске `script_fields`) против живых атрибутов инстанса (переприменяются каждый кадр из `_field_values`, списки конвертируются в `Vec2/3/4` только при точной аннотации). Фикс: из инструментов используйте `get_field_value`/`set_field_value`, держите аннотации точными (`x: Vec3`, а не `x = [0,0,0]`), а на диске ждите `script_fields`, не `_field_values`:

```python
comp.set_field_value("speed", 7.5)
speed = comp.get_field_value("speed")
```

## 10. Углы в радианах ничего толком не дают

Симптом: `rotate(Vec3(0, 0.05, 0))` едва поворачивает. Причина: `Transform.rotate` принимает **градусы** (конверсия радиан→градусов внутри). Фикс: передавайте градусы, конвертируйте на границе:

```python
t.rotate(Vec3(0.0, 45.0 * dt, 0.0))   # 45 град/сек, не 45 рад/сек
```

## 11. `GetMouseButton(1000)` никогда не срабатывает

Симптом: проверки мыши с `KeyCode.MOUSE_LEFT` не стреляют. Причина: `GetMouseButton(button)` прибавляет **индекс** к `MOUSE_LEFT` — передавайте `0`, а не `1000`. Фикс: `Input.GetMouseButtonDown(0)` для левой. (Альтернатива через коды: `Input.GetKey(KeyCode.MOUSE_LEFT)`.)

## 12. Ошибки называют метод, но не строку

Симптом: в консоли `Script on_update error 'x.py': ...` без места. Причина: `_call_py` пишет только `str(e)`. Фикс: разрабатывайтесь с кнопкой Check (диагностика `error [line N]`) и оборачивайте рискованный покадровый код логированием `traceback.format_exc()` (см. `debugging.md`).

## Связанные страницы

- `debugging.md` — форматы Check, уровни Logger, fallback старой версии.
- `input-reference.md` — полная таблица кодов за пунктами 3, 5, 11.
- `templates.md` — исправленные версии паттернов, которые обычно пишут неправильно.

## Проверено по коду

- Поверхность `Vec3` (нет `rotate`; есть `lerp/to_list/zero/one/up/right/forward`) + `Quat.from_axis_angle/rotate_vec3/look_rotation`.
- `core/components/scripting/scriptc/hotreload.py:202-207` (скан `dir(mod)`), `:253-260` (логирование `str(e)`), `:128-133` (проброс `DefineAxis`).
- `core/input/inpsys/gamepad.py:45-61` (`MOUSE_LEFT + button`), `:99-126` (`0.0` для неизвестных осей), `:128-136` (define-вызовы).
- `core/components/audio/source/component.py:78-84` (автостарт), `core/ecs/scene.py:229-239` (голая сущность), `core/components/transform/tr/matrix.py:355-364` (градусы).
- `core/components/scripting/cythonp/loader.py:56-103` (синхронная pyximport-сборка), `:189-242` (prepare/snapshot/changed), `core/components/scripting/scriptc/component.py:132-150` (`script_fields`), `fields.py:170-214` (хранилища полей + Vec-конверсия).
