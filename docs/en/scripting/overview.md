# Scripting Overview

Entity behaviors in ZarinEngine are plain `.py` files with a behavior class. No base class, no registration boilerplate.

Create a script via `Project → Create → Python Script`, attach it to an entity via `Inspector → Add Component → Scripts`, press `Play`.

```python
from core.maths.math3d import Vec3


class Mover:
    speed: float = 5.0

    def on_update(self, dt: float):
        t = self._entity.transform
        if t:
            t.translate(Vec3(0.0, 0.0, self.speed * dt))
```

How it works:

- The engine finds the first class defined in the file that has at least one lifecycle callback (or `_inspector_buttons`). Inheritance is not required.
- The constructor is a plain no-argument `__init__`. The engine sets `self._entity` before every call.
- Short names are full aliases: `update` = `on_update`, `start` = `on_start`, and so on. If both variants are defined, the `on_*` version wins.
- One entity can hold several `ScriptComponent` instances (`_allow_multiple = True`).
- An exception inside any callback never crashes the game: it is written to the console with the script and method name, and the frame continues.

Subpages:

- Lifecycle Callbacks — full table of methods and signatures.
- Inspector Fields and Range — how class annotations become editor widgets.
- Input and Math API — what is auto-injected into every script.
- Hot Reload — editing scripts while Play is running.
- Cython in Scripts — moving heavy math to compiled modules.
- ScriptComponent — the engine-side component: paths, flags, class resolution, Check.
- Script Examples — copy-paste behaviors.
