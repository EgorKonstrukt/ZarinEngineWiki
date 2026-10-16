# Installation

> Install ZarinEngine from a checkout: install Python packages, run `python main.py`, let native extensions build once.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

There is no installer. The engine runs from source: `main.py` is the editor, `player.py` is the runtime. First start compiles Cython extensions in place (`setup.py build_ext --inplace`); a splash screen shows per-module progress.

## Properties

| name | type | default | description |
|---|---|---|---|
| install_cmd | command | `pip install -r requirements.txt` | Installs all Python dependencies |
| editor_cmd | command | `python main.py` | Starts the editor from the engine root |
| scene_arg | CLI arg | `main.py [file.zpes]` | Opens a `.zpes` scene on start (or forwards it to a running instance via IPC); `.zterr` terrain graphs open in the terrain editor |
| global_settings | path | `~/.zarin/settings.json` | Created on first run; stores global `Config` defaults |
| ext_rebuild | automatic | on start if missing/stale | `main.py` compares `core/pyx/*.pyx` mtime against built extensions |

## Usage in Editor

Not applicable — this page gets you into the editor.

## Usage in Code

```bash
pip install -r requirements.txt
python main.py
```

```bash
# Open a scene directly (second launch forwards to the running editor):
python main.py scenes/SampleScene.zpes
```

## Serialization

Not applicable.

## Related pages

- [Requirements](./requirements.md)
- [Quick Start](./quick-start.md)
- [Editor Tour](./editor-tour.md)
- [Configuration](../core/config.md)

## Verified against

- `README.md` (Quick start section)
- `main.py` (argparse `file` arg, `IpcServer`, `_check_extensions_missing`)
- `setup.py` (delegates to `core.build_cython.build`)
- `core/config/config.py` (`get_global_config` path `~/.zarin/settings.json`)
