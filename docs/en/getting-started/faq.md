# FAQ

> Short answers to repeated setup and runtime questions. Each answer cites the code that defines the behavior.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

**Q: How do I start the editor?**
A: `pip install -r requirements.txt`, then `python main.py` from the engine root (`README.md`, `main.py`).

**Q: How do I run a scene without the editor?**
A: `python player.py <scene.zpes>`. Resolution order: CLI argument → first entry of `BuildSettings.json` `scenes` → `ProjectSettings.json` `project.default_scene` → `scenes/SampleScene.zpes` (`player.py:_resolve_startup_scene`).

**Q: Which Python version?**
A: 3.13+ (`README.md`). Cython extensions rebuild automatically on editor start if stale.

**Q: My script does nothing. Why?**
A: Scripts run only in Play mode. The engine uses the first class in the file that defines a lifecycle callback (`on_update`, `on_start`, …); short aliases (`update`, `start`) also work. Exceptions in callbacks are logged, never fatal (`docs/scripting.md`, `core/ecs/query.py`).

**Q: Script edits during Play?**
A: Hot-reload recreates the instance on the next frame, keeps inspector values, re-runs `on_awake`/`on_start`. Disable with `hot_reload = False` on `ScriptComponent` (`docs/scripting.md`).

**Q: Where are settings stored?**
A: Global: `~/.zarin/settings.json`. Per-project: `<project>/ProjectSettings.json` (`core/config/config.py`).

**Q: Editor window is blank on Wayland?**
A: `main.py` forces `QT_QPA_PLATFORM=xcb` when a Wayland session is detected, for OpenGL compatibility.

**Q: How do I ship a game?**
A: `python build_nuitka.py` (configuration in `BuildSettings.json`; `README.md`, `build_nuitka.py` → `tools.nuitka.build`).

## Properties

| name | type | default | description |
|---|---|---|---|
| support_channel | file | `editor/bug_report.py` | `main.py`/`player.py` install bug-report hooks at startup |

## Usage in Editor

Not applicable.

## Usage in Code

Not applicable.

## Serialization

Not applicable.

## Related pages

- [Requirements](./requirements.md)
- [Installation](./installation.md)
- [Quick Start](./quick-start.md)
- [Configuration](../core/config.md)
- [Logging](../core/logging.md)

## Verified against

- `README.md`, `main.py`, `player.py`, `build_nuitka.py`
- `docs/scripting.md` (contract, hot-reload, `Check` button)
- `core/config/config.py` (settings paths)
- `core/ecs/query.py` (callback error isolation)
