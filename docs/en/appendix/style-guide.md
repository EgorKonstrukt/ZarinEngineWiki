# Style Guide (Contribution Contract)

> Enforced by `py tools/arch_gate.py`. Source: `ARCHITECTURE.md` + `tools/arch_gate.py`.

[Home](../../index.md)

## Layers (dependencies only downward)

```
main.py / player.py            entry points
editor/                        editor (PySide6): panels, viewport, inspector
plugins/                       plugins (VR, physics, tracker, plotter, MCP)
core/components/               ECS components (data + serialization)
core/renderer/ core/physics/
core/audio/ core/network/      subsystems (logic, no Qt)
core/ecs/                      Entity / Component / Scene / ComponentRegistry
core/foundation/               logger, config, commands, profiler, plugins
core/spatial/ core/maths/      math and data structures (numpy float64)
```

`core/` never imports `editor` or `plugins`; upward communication only via `Engine.on/emit` events and callbacks (`ARCHITECTURE.md:19`).

## Modules

- One file = one responsibility, **≤ 450 lines**, one public class (+ private helpers). Exception: `editor/panels/project/*` up to 500 lines — allowlist `ALLOW_BIG` (`arch_gate.py:25-35,39`): `panel.py`, `pane.py`, `widgets.py`, `fs_ops.py` + vendored `plugins/tracker_music_plugin/libs/nodmod/{xmsong,modsong,song,s3msong,types}.py`.
- Package assembly via mixins: `component.py` (skeleton) + `solver.py`/`draw.py`/… + `__init__.py` with re-export (`ARCHITECTURE.md:25`).
- Public API only through the package `__init__.py`; package internals are private. Canonical examples (`ARCHITECTURE.md:27-30`): `from core.ecs import Component, Entity, Scene, ComponentRegistry`; `from core.components.transform.tr import Transform`; `from editor.panels.project import ProjectPanel`. The gate resolves top-level imports against `core/editor/plugins/physics_solvers/tools/tests/playerp/nodmod/main/player` plus `EXTENSION_SUFFIXES` (`arch_gate.py:250-274`).

## Components

- Inherit `Component`; `@ComponentRegistry.register` is mandatory or the component is invisible (gate checks every `Component` subclass except `*Mixin*`, `*Base*`, `_private`, plus `ALLOW_UNREGISTERED = {"SkyIbl", "RaytraceComponent"}` — `arch_gate.py:277-306`).
- Serialization pair `serialize`/`deserialize`; inspector via `_inspector_fields`.
- Lifecycle: `on_awake` → `on_start` → `on_update(dt)` / `on_fixed_update(dt)` → `on_destroy` (`ARCHITECTURE.md:36`).

## Code style

- License header is the first line. No comments; docstrings only where the contract is unclear without one (`ARCHITECTURE.md:40`). Gate: `check_comments` flags any `#` comment after code starts; `check_docstrings` flags module/class/function string-literal first statements (`arch_gate.py:88-120`). Exempt: `main.py`/`player.py`, vendored `libs/nodmod`, tests (`ARCHITECTURE.md:42`).
- Colors/shaders without `#` in source — use `chr(35)`, `rgb()`; runtime values stay 1:1 (`ARCHITECTURE.md:41`, e.g. `menup/builder.py:33` `_qta = lambda n, c=chr(35)+"d4d4d4": …`).
- Gate also checks: `py_compile` of every `.py` (excluding `.venv/__pycache__/NodeGraphQt/.git/build/dist/linux/~`), file size, undefined names (allowing `__compiled__`), and import resolvability (`arch_gate.py:42-51,123-274`).

## Verification

```bash
py tools/arch_gate.py            # compile, comments, docstrings, size, names, imports, registry
py tools/arch_gate.py --smoke    # gate + whole tests/smoke suite
py tests/smoke/run_smokes.py     # full suite standalone (fresh process per test, QT_QPA_PLATFORM=offscreen)
```

Gate prints `GATE OK`/`GATE FAIL` (`arch_gate.py:348`); `--smoke` tolerates only `KNOWN_SMOKE_FAILS = {"test_compression_async.py", "test_prop_picker.py"}` (`arch_gate.py:38`). Pre-existing, non-regression causes (`ARCHITECTURE.md:52-54`): `test_compression_async.py` — 150 KB budget unachievable at `fast` level (minimum 159 KB); `test_prop_picker.py` — the `.anim` clip has no position curves, so waiting for `y=0.1413` has nothing to fire on.

## Verified against

- `ARCHITECTURE.md` (layers, 450/500 rule, mixins, imports, lifecycle, style, gate commands, 2 known failures)
- `tools/arch_gate.py:17-39` (SKIP_TOPS, ALLOW_BIG, ALLOW_UNDEF, ALLOW_UNREGISTERED, KNOWN_SMOKE_FAILS, MAX_LINES), `:42-134` (file walk, compile/comments/docstring/size), `:213-348` (imports, registry, smoke, GATE output)
- `editor/main_window/menup/builder.py:33` (`chr(35)` color idiom)
