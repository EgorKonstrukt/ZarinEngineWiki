# Quick Start

> Open the editor, press Play, attach a script, run a scene in the player. Five minutes from zero to a rotating object.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

The loop is: editor (`main.py`) for authoring, Play mode for testing, `player.py` for running a scene without the editor. Scripts are plain `.py` files; entity behavior needs no base class.

## Properties

| name | type | default | description |
|---|---|---|---|
| editor | command | `python main.py` | Authoring environment |
| play | editor button | Play | `Engine.start_play()` → `Scene.start()` → per-frame `tick()` |
| player | command | `python player.py <scene.zpes>` | Standalone run; scene resolution: CLI arg → `BuildSettings.json` scenes[0] → `ProjectSettings.json` `project.default_scene` → `scenes/SampleScene.zpes` |
| build | command | `python build_nuitka.py` | Standalone executable (settings in `BuildSettings.json`) |

## Usage in Editor

1. `python main.py`.
2. Create an entity in the Hierarchy (the Hierarchy context menu auto-adds a `Transform`; bare `Scene.create_entity()` in code does not).
3. `Project → Create → Python Script`, attach via `Inspector → Add Component → Scripts`.
4. Press Play. Edit the script file during Play: the instance is recreated next frame, inspector values are kept (`hot_reload`).

## Usage in Code

```python
from core.maths.math3d import Vec3


class Rotator:
    speed: float = 90.0

    def on_update(self, dt: float):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

```python
# Headless equivalent of pressing Play on the current scene:
engine.start_play()   # Engine.start_play(); Engine.stop_play() to exit
```

## Serialization

Script field values are stored in the scene. Example component entry:

```json
{ "type": "ScriptComponent", "enabled": true, "script_path": "assets/move.py",
  "script_fields": { "speed": 90.0 } }
```

## Screenshots / Media

![ZarinEngine editor main window](../../assets/screenshots/editor-main.png)

> What you should see after `python main.py`: the editor main window.
> File: `assets/screenshots/editor-main.png` (verified copy of engine
> `zarin_screenshot.png`). Screen recordings: no `.gif` files exist in the
> engine repo yet — see `assets/gifs/README.md` for how to record
> (ScreenToGif/Peek/LICEcap, 800 px width, < 5 MB);
> put finished GIFs in `assets/gifs/`.

## Related pages

- [First Scene](./first-scene.md)
- [Editor Tour](./editor-tour.md)
- [Entity](../core/entity.md)
- [Transform](../core/transform.md)
- [Scripting](../scripting/overview.md)
- [Player and Nuitka Build](../build/player-nuitka.md)

## Verified against

- `README.md` (Quick start, Scripting sample)
- `player.py` (`_resolve_startup_scene`, `_deduce_project_root`, `DEFAULT_SCENE = "SampleScene.zpes"`)
- `core/engine/eng/engine.py` (`start_play`, `stop_play`)
- `docs/scripting.md` (script contract, hot-reload)
- `core/components/scripting/scriptc/component.py` (`script_path`, `script_fields`)
