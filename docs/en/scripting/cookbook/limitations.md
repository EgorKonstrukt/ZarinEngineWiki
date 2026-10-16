# Honest Scripting Limitations

What the engine does *not* give you — each item verified against the source, with the supported alternative.

## 1. No `Vec3.rotate`

`Vec3` (`core/pyx/_math_vec.pyx`) exposes `dot`, `cross`, `length`, `length_sq`, `normalized`, `to_list`, `zero`, `one`, `up`, `forward`, `right` — there is no `rotate` method. Rotating a free vector is `Quat.rotate_vec3(v)` (same file). Rotating an object is `Transform.rotate(euler: Vec3)` taking **euler degrees** (`core/components/transform/tr/matrix.py:355`).

```python
t.rotate(Vec3(0.0, 90.0 * dt, 0.0))  # yaw 90 deg/sec, NOT t.rotate(vector)
```

## 2. `_call_py` logs errors without a traceback

`core/components/scripting/scriptc/hotreload.py:253-260`:

```python
def _call_py(self, canonical, *args):
    ...
    try:
        meth(*args)
    except Exception as e:
        Logger.error(f"Script {canonical} error '{self.script_path}': {e}")
```

Only `str(e)` is logged — no traceback, no line number. When a callback silently stops working, wrap suspect blocks yourself and log with `traceback.format_exc()`, or reproduce the logic outside Play mode.

## 3. Named axes return `0.0` until you `DefineAxis`

`Input.GetAxis(name)` (`core/input/inpsys/gamepad.py`) returns `0.0` for any name missing from `cls._state._axes` — and mouse axes only after `DefineMouseAxis`. There are no pre-registered `Horizontal`/`Vertical` axes: call `Input.DefineAxis("Horizontal", positive=[...], negative=[...], ...)` (key codes from `KeyCode`) before first use, once (e.g. in `on_awake`).

```python
def on_awake(self):
    Input.DefineAxis("Horizontal", [KeyCode.D], [KeyCode.A])
    Input.DefineAxis("Vertical", [KeyCode.W], [KeyCode.S])

def on_update(self, dt):
    x = Input.GetAxis("Horizontal")  # 0.0 unless defined above
```

## 4. `AudioSource.on_enable` auto-starts playback

`core/components/audio/source/component.py:82-84`:

```python
def on_enable(self):
    if self.clip_path and not self._playing:
        self.play()
```

Any disable→enable toggle restarts the clip (in addition to `play_on_awake` in `on_start`). `on_disable`/`on_destroy` stop the voice. Workaround: keep `clip_path` empty until you explicitly `play()`, or gate enables behind your own flag.

## 5. A neighbouring `.pyx` triggers a blocking build

Hot-reload watches Cython neighbours (`ext_sources_changed`, `unload_ext_modules` in `hotreload.py`); a stale `.pyx` is rebuilt synchronously by `ensure_extensions(plugin_dir, background=False)` with a per-file timeout (`core/foundation/plugin_cython.py`), and loaded via `import_extension(package, module, fallback_dir)` from the adjacent `.pyd`/`.so`. Keep script-side `.pyx` files small, or ship the prebuilt extension so frames never stall on a compile.

## 6. Threads: never touch Qt / GL / scene objects from workers

Docks are `QTimer`-driven widgets and the viewport renders GL on the main Qt thread (`vr_dock.py`, `physviz/plugin.py`, `plotter_plugin/panel.py`). The codebase's own crossing pattern is a `QueuedConnection` dispatcher — `plugins/zarin_mcp/main_thread.py`:

```python
def run_on_main_thread(fn, timeout_ms=60000): ...
```

Compute on worker threads, then marshal *all* engine/scene/Qt calls through main-thread dispatch. There is no thread-safe scene API.

## Verified against

`core/pyx/_math_vec.pyx`, `core/components/transform/tr/matrix.py`, `core/components/scripting/scriptc/hotreload.py`, `core/input/inpsys/gamepad.py`, `core/components/audio/source/component.py`, `core/foundation/plugin_cython.py`, `plugins/zarin_mcp/main_thread.py`.
