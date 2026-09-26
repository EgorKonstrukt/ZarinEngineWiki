# Lifecycle Callbacks

Every callback is optional. Define only what the behavior needs; the engine caches per-method lookups and skips missing ones without cost.

| Method | Short alias | Signature | When it runs |
|---|---|---|---|
| `on_awake` | `awake` | `()` | Once when Play starts, before `on_start`; use for caches and peer lookups |
| `on_start` | `start` | `()` | Once when Play starts; use for initial state and first actions |
| `on_update` | `update` | `(dt)` | Every rendered frame in scene order; `dt` already scaled by time scale |
| `on_fixed_update` | `fixed_update` | `(dt)` | Every physics step with stable `dt` (`0.02` default); forces and movement here |
| `on_enable` | `enable` | `()` | When the entity becomes active (`entity.active = True`) |
| `on_disable` | `disable` | `()` | When the entity becomes inactive; stop loops and sounds here |
| `on_collision_enter` | — | `(other_id)` or `(other_id, force)` | Contact starts |
| `on_collision_stay` | — | `(other_id)` or `(other_id, force)` | Contact continues |
| `on_collision_exit` | — | `(other_id)` or `(other_id, force)` | Contact ends |
| `on_destroy` | `destroy` | `()` | Component removed or scene unloaded; release resources here |
| `gizmo_lines` | — | `() -> list` | Editor gizmo lines, runs outside Play too |
| `gizmo_meshes` | — | `() -> list` | Editor gizmo meshes, runs outside Play too |

Call order at Play start: `on_awake` for every scripted component, then `on_start` for every scripted component. Do not assume sibling start order — fetch peers lazily or in `on_update` guards.

`dt` semantics: render `dt` stretches with slow motion and pause (time scale 0 freezes updates); fixed `dt` stays constant so physics and its scripts stay deterministic.

Collision dispatch: the solver reports contacts to the physics layer, which maps solver bodies to entity ids and invokes callbacks. Arity is measured once from the signature — two parameters receive `(other_id: str, force: float)`, one parameter receives just `other_id`. Resolve the other entity inside the callback only if needed; id comparison is cheaper.

Enable/disable: toggling `entity.active` fires `on_disable`/`on_enable` on all components of that entity, scripts included. Disabling mid-Play pauses that behavior without destroying its instance state.

Destroy cases: removing the ScriptComponent in the editor, deleting the entity, unloading the scene, or stopping Play. Hot-reload recreates the instance but preserves Inspector field values and re-runs `on_awake`/`on_start`.

Exceptions: any error in any callback is caught and logged as `Script ... error ...` with a traceback naming the script and method. The frame, the physics step and the remaining components continue.

```python
class Fighter:
    health: int = 100

    def on_awake(self):
        self.hits = 0
        self.body = self._entity.get_component_by_name("Rigidbody")

    def on_start(self):
        self.alive = True

    def on_update(self, dt):
        if self.health <= 0 and self.alive:
            self.alive = False
            self._entity.active = False

    def on_fixed_update(self, dt):
        if self.body and self.health <= 0:
            self.body.velocity = Vec3(0.0, 0.0, 0.0)

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        self.health = self.health - int(force)

    def on_destroy(self):
        self.hits = 0
        self.body = None
```
