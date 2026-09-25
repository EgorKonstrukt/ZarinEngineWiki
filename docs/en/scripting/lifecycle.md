# Lifecycle Callbacks

Every callback is optional. Define only what the behavior needs.

| Method | Short alias | Signature | When it runs |
|---|---|---|---|
| `on_awake` | `awake` | `()` | Once when Play starts, before `on_start` |
| `on_start` | `start` | `()` | Once when Play starts |
| `on_update` | `update` | `(dt)` | Every rendered frame, `dt` already multiplied by time scale |
| `on_fixed_update` | `fixed_update` | `(dt)` | Every physics step (`dt = 0.02` by default) |
| `on_enable` | `enable` | `()` | When the entity becomes active |
| `on_disable` | `disable` | `()` | When the entity becomes inactive |
| `on_collision_enter` | — | `(other_id)` or `(other_id, force)` | Contact starts |
| `on_collision_stay` | — | `(other_id)` or `(other_id, force)` | Contact continues |
| `on_collision_exit` | — | `(other_id)` or `(other_id, force)` | Contact ends |
| `on_destroy` | `destroy` | `()` | When the component is removed or the scene unloads |
| `gizmo_lines` | — | `() -> list` | Editor gizmo lines, works outside Play too |
| `gizmo_meshes` | — | `() -> list` | Editor gizmo meshes, works outside Play too |

Rules:

- Aliases are resolved per method with `on_*` priority: if a class defines both `update` and `on_update`, only `on_update` runs.
- Collision callbacks accept one or two arguments. The engine inspects the signature once: with two parameters the second one receives the impact force as `float`, otherwise only the other entity id (`str`) is passed.
- `self._entity` is assigned by the engine before every call, including `on_awake` and `on_start`.
- Any exception in a callback is caught and logged as `Script ... error ...` with a traceback. The frame continues with the next component.

```python
class Fighter:
    health: int = 100

    def on_awake(self):
        self.hits = 0

    def on_update(self, dt):
        if self.health <= 0:
            self._entity.active = False

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        self.health = self.health - int(force)

    def on_destroy(self):
        self.hits = 0
```
