# Getting Started

> Entry point to ZarinEngine: a Python ECS game engine with a visual editor. Install dependencies, open the editor with `python main.py`, run scenes with `python player.py`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

ZarinEngine is a 3D game and simulation engine written in Python (ECS architecture, ModernGL rendering, PySide6 editor). The API is Unity-inspired: scene hierarchy, components, inspector, prefabs. License: Mozilla Public License 2.0.

## Properties

| name | type | default | description |
|---|---|---|---|
| entry_editor | command | `python main.py` | Opens the visual editor (see `main.py`) |
| entry_player | command | `python player.py <scene.zpes>` | Runs a built game from a scene file (see `player.py`) |
| entry_build | command | `python build_nuitka.py` | Compiles a standalone executable via Nuitka |
| scene_extension | str | `.zpes` | Scene file (JSON) |
| language | str | Python 3.13+ | Only language needed; no compilation for iteration |

## Usage in Editor

1. Install dependencies, run `python main.py`.
2. Read [Requirements](./requirements.md), then [Installation](./installation.md).
3. Follow [Quick Start](./quick-start.md), tour the UI in [Editor Tour](./editor-tour.md).
4. Build your [First Scene](./first-scene.md). Open [FAQ](./faq.md) on failures.

## Usage in Code

```python
# No project setup is required. From the engine root:
#   pip install -r requirements.txt
#   python main.py                  # editor
#   python player.py SampleScene.zpes  # run a scene
```

## Serialization

Not applicable. Scene persistence is covered in [Registry and Serialization](../core/registry-serialization.md) and [Scenes](../scenes-prefabs/scenes.md).

## Related pages

- [Requirements](./requirements.md)
- [Installation](./installation.md)
- [Quick Start](./quick-start.md)
- [Editor Tour](./editor-tour.md)
- [First Scene](./first-scene.md)
- [FAQ](./faq.md)
- [ECS Overview](../core/ecs-overview.md)
- [Scripting](../scripting/overview.md)

## Verified against

- `README.md` (features, quick start, requirements, license)
- `main.py` (editor entry point)
- `player.py` (player entry point, `DEFAULT_SCENE`)
- `build_nuitka.py` (build entry point)
- `LICENSE` (MPL-2.0)
