# Collision Callbacks

Three phases derived from frame-to-frame contact sets: `on_collision_enter` (new pairs), `on_collision_stay` (kept pairs), `on_collision_exit` (lost pairs). Both bodies are notified with the *other* entity id.

## Properties

| Item | Value |
|---|---|
| Phases | `enter` / `stay` / `exit`, computed as `entered = current − prev`, `exited = prev − current`, `stayed = current ∩ prev` (`events.py:305-307`) |
| Script signature | `other_id` OR `(other_id, force)` — arity from `inspect.signature` at load |
| Component signature | Always `(other_id, force)` |
| `force` | `float(ev.get("force", 0.0))`, max over duplicate events for the pair |
| Gating | `_has_collision_listeners()` skips the whole pass when nobody listens |

## Arity rule (scripts)

At script load (`hotreload.py:240-248`):

```python
nargs = len(inspect.signature(meth).parameters)  # fallback 1 on error
self._py_collision_arity[cb] = 2 if nargs >= 2 else 1
```

Dispatch (`events.py:317-336`): arity `>= 2` → `callback(other_id, force)`, else `callback(other_id)`. Components (`_invoke_component_collision`, `events.py:356-367`) always get `(other_id, force)` and `ScriptComponent` instances are skipped there (they are handled by the script path).

## Usage in Code

```python
# scripts: 1-arg form also works — force is only passed when declared
def on_collision_enter(self, other_id):
    print("hit:", other_id)

def on_collision_stay(self, other_id, force=0.0):
    print("stay:", other_id, force)

def on_collision_exit(self, other_id):
    print("left:", other_id)
```

```python
# plain components must accept both args:
class DamageOnHit(Component):
    def on_collision_enter(self, other_id, force):
        ...
```

## Fixed update notes

- Events come from `solver.get_collision_events()` each physics step; `prev_frame_contacts` is then replaced by the current set (`events.py:348-354`).
- Listeners are re-scanned when the entity/component count signature changes (`_listener_signature`).

## Related

Collision Layers, Rigidbody, Solver & Threading, Script Component, Physics 2D Overview (same callbacks).

## Verified against

- `core/physics/scene/events.py:255-367` (listener check, set diff, both dispatchers)
- `core/components/scripting/scriptc/hotreload.py:240-248` (`inspect.signature` arity)
- `tests/smoke/test_scripting.py:233-248` (arity `1` vs `2` expectations)
