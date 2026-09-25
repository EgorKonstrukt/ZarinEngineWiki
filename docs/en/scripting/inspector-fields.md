# Inspector Fields and Range

Annotated class attributes automatically appear in the Inspector, and their values are stored in the scene (`.zpes`). Private names starting with `_` are skipped, except the special `_inspector_buttons`.

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

| Annotation | Inspector widget |
|---|---|
| `float` | Number field |
| `int` | Integer field |
| `bool` | Checkbox |
| `str` | Text field |
| `Annotated[float, Range(min, max[, step])]` | Float slider, default step `0.01` |
| `Annotated[int, Range(min, max)]` | Integer slider, default step `1` |
| `Vec2` / `Vec3` / `Vec4` | Vector fields |
| `Enum` subclass | Dropdown list |
| `'Entity'` | Entity picker (UUID is stored, the live object is passed to the script) |
| `Curve` | Curve editor |

Notes:

- `Range` is `Range(min_value=0.0, max_value=1.0, step=None)`. A plain `[min, max]` or `(min, max[, step])` sequence in `Annotated` metadata works the same way.
- Resource pickers (mesh, material, texture, audio, prefab, scene, physics material) use the same dialog filters as built-in components.
- `_inspector_buttons = [(method_name, label), ...]` renders buttons that call the method on the live instance.
- The top of the component shows an immutable `Script` field with the file name: click reveals the file in the Project panel, the `...` button opens the built-in script editor.
