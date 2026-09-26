# Inspector Fields and Range

Annotated class attributes automatically appear in the Inspector, and their values are stored in the scene (`.zpes`). Class-level defaults become the initial values; per-instance edits override them without touching the `.py` file. Private names starting with `_` are skipped, except the special `_inspector_buttons`.

```python
from enum import Enum
from typing import Annotated
from core.maths.math3d import Vec3
from core.foundation.curve import Curve


class Mode(Enum):
    IDLE = 0
    PATROL = 1


class Enemy:
    speed: Annotated[float, Range(0.0, 10.0)] = 5.0
    health: int = 100
    title: str = "grunt"
    active: bool = True
    offset: Vec3 = Vec3(0.0, 1.0, 0.0)
    mode: Mode = Mode.PATROL
    falloff: Curve = Curve()
    target: 'Entity' = None
    _inspector_buttons = [("explode", "Explode")]

    def explode(self):
        self.health = 0
```

| Annotation | Inspector widget | Stored as |
|---|---|---|
| `float` | Number field | float |
| `int` | Integer field | int |
| `bool` | Checkbox | bool |
| `str` | Text field | string |
| `Annotated[float, Range(min, max[, step])]` | Float slider, default step `0.01` | float clamped to range |
| `Annotated[int, Range(min, max)]` | Integer slider, default step `1` | int clamped to range |
| `Vec2` / `Vec3` / `Vec4` | Vector fields | float components |
| `Enum` subclass | Dropdown of member names | enum value |
| `'Entity'` | Entity picker | UUID; the live object is passed to the script |
| `Curve` | Curve editor | keyframe set |

Details:

- `Range` is `Range(min_value=0.0, max_value=1.0, step=None)`. A plain `[min, max]` or `(min, max[, step])` sequence in `Annotated` metadata works identically.
- Entity picker stores the target UUID in the scene and resolves the live object before each call — safe across renames, unlike name-based lookup.
- Resource-typed fields (mesh, material, texture, audio, prefab, scene, physics material) reuse the built-in picker dialogs and their file filters: models `*.obj *.fbx *.stl *.gltf *.glb *.usdz *.dae *.3ds *.blend`, materials `*.zpem *.mat`, images `*.png *.jpg *.jpeg *.bmp *.tga *.tif *.tiff *.webp *.hdr *.exr *.dds *.svg`, audio `*.wav *.mp3 *.ogg *.flac *.aiff *.m4a`, scripts `*.py`, prefabs `*.zpep`, scenes `*.zpes`, animation clips and controllers, physics materials `*.zphysmat`.
- `_inspector_buttons = [(method_name, label), ...]` renders buttons that invoke the method on the live Play instance (or the editor instance outside Play) — handy for test triggers like explode, reset or spawn.
- The component header shows an immutable `Script` field with the file display name: click reveals the file in the Project panel, the `...` button opens the built-in script editor. Changing the file rebuilds the field list; values for surviving names are kept.
- Hot-reload re-applies Inspector values onto the recreated instance, so tuning survives code edits.

Guidance: expose designers' knobs (speeds, counts, toggles) as fields, keep internal caches (`self._cache`, cooldowns) as plain un-annotated attributes set in `on_awake`.
