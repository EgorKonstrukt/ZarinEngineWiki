# Честные ограничения скриптинга

Чего движок *не* даёт — каждый пункт проверен по исходникам, везде есть рабочая альтернатива.

## 1. Нет `Vec3.rotate`

У `Vec3` (`core/pyx/_math_vec.pyx`) есть `dot`, `cross`, `length`, `length_sq`, `normalized`, `to_list`, `zero`, `one`, `up`, `forward`, `right` — метода `rotate` нет. Поворот свободного вектора — `Quat.rotate_vec3(v)` (тот же файл). Поворот объекта — `Transform.rotate(euler: Vec3)` с **эйлеровыми градусами** (`core/components/transform/tr/matrix.py:355`).

```python
t.rotate(Vec3(0.0, 90.0 * dt, 0.0))  # рысканье 90 град/с, НЕ t.rotate(вектор)
```

## 2. `_call_py` логирует ошибки без трейсбека

`core/components/scripting/scriptc/hotreload.py:253-260`:

```python
def _call_py(self, canonical, *args):
    ...
    try:
        meth(*args)
    except Exception as e:
        Logger.error(f"Script {canonical} error '{self.script_path}': {e}")
```

Логируется только `str(e)` — без трейсбека и номера строки. Если колбэк молча перестал работать, оборачивайте подозрительные места сами и логируйте с `traceback.format_exc()` либо воспроизводите логику вне Play.

## 3. Именованные оси возвращают `0.0`, пока не вызван `DefineAxis`

`Input.GetAxis(name)` (`core/input/inpsys/gamepad.py`) возвращает `0.0` для любого имени, отсутствующего в `cls._state._axes`, — а оси мыши только после `DefineMouseAxis`. Предустановленных осей `Horizontal`/`Vertical` нет: вызовите `Input.DefineAxis("Horizontal", positive=[...], negative=[...], ...)` (коды из `KeyCode`) до первого использования, один раз (например, в `on_awake`).

```python
def on_awake(self):
    Input.DefineAxis("Horizontal", [KeyCode.D], [KeyCode.A])
    Input.DefineAxis("Vertical", [KeyCode.W], [KeyCode.S])

def on_update(self, dt):
    x = Input.GetAxis("Horizontal")  # 0.0, если не определено выше
```

## 4. `AudioSource.on_enable` сам стартует проигрывание

`core/components/audio/source/component.py:82-84`:

```python
def on_enable(self):
    if self.clip_path and not self._playing:
        self.play()
```

Любой тоггл disable→enable перезапускает клип (в дополнение к `play_on_awake` в `on_start`). `on_disable`/`on_destroy` голос останавливают. Обход: держите `clip_path` пустым, пока явно не вызовете `play()`, либо гейтите включения своим флагом.

## 5. Соседний `.pyx` вызывает блокирующую сборку

Hot-reload следит за Cython-соседями (`ext_sources_changed`, `unload_ext_modules` в `hotreload.py`); несвежий `.pyx` пересобирается синхронно через `ensure_extensions(plugin_dir, background=False)` с таймаутом на файл (`core/foundation/plugin_cython.py`), а грузится через `import_extension(package, module, fallback_dir)` из соседнего `.pyd`/`.so`. Держите скриптовые `.pyx` маленькими либо поставляйте предсобранное расширение, чтобы кадры не вставали на компиляции.

## 6. Потоки: не трогайте Qt / GL / объекты сцены из воркеров

Доки — виджеты на `QTimer`, вьюпорт рендерит GL в главном потоке Qt (`vr_dock.py`, `physviz/plugin.py`, `plotter_plugin/panel.py`). Штатный паттерн пересечения потоков в кодовой базе — диспетчер `QueuedConnection`, `plugins/zarin_mcp/main_thread.py`:

```python
def run_on_main_thread(fn, timeout_ms=60000): ...
```

Считайте в воркерах, а *все* вызовы движка/сцены/Qt маршальте через диспетчер главного потока. Потокобезопасного API сцены нет.

## Проверено по коду

`core/pyx/_math_vec.pyx`, `core/components/transform/tr/matrix.py`, `core/components/scripting/scriptc/hotreload.py`, `core/input/inpsys/gamepad.py`, `core/components/audio/source/component.py`, `core/foundation/plugin_cython.py`, `plugins/zarin_mcp/main_thread.py`.
