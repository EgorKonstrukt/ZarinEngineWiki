# Components and Lifecycle

> A `Component` is data plus lifecycle hooks. Subclass it, decorate with `@ComponentRegistry.register`, implement `serialize`/`deserialize`, and the engine calls `on_awake → on_start → on_update(dt) / on_fixed_update(dt) → on_destroy`.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

`Component.__init_subclass__` auto-detects per-frame participation: `_updates` is `True` when the subclass overrides `on_update`, `_fixed_updates` when it overrides `on_fixed_update`. Only active-entity, `enabled` components sit in the scene's dispatch sets; the `enabled` setter syncs them immediately. `on_awake` runs on `add_component` (even in edit mode); `on_start` runs once per `Scene.start()` (i.e. Play start). `Scene.update` partitions components into constraints → others → IK; `Scene.fixed_update` iterates the cached fixed set in set order (no guaranteed insertion order). Callback exceptions are caught per component and logged as `Update error in <entity>/<component>` (same for fixed). `ScriptComponent._allow_multiple = True`; most built-ins are single-instance.

## Properties

| name | type | default | description |
|---|---|---|---|
| enabled | bool | `True` | Setter adds/removes the component from scene dispatch sets |
| entity | Entity\|None | `None` | Owning entity (set by `add_component`) |
| transform | Component\|None | cached | Owning entity's `Transform`, cached per component |
| _allow_multiple | bool | `False` | `True` → key `Class.<8-hex>`; e.g. `ScriptComponent`, colliders |
| _updates | bool | auto | `True` if subclass overrides `on_update` |
| _fixed_updates | bool | auto | `True` if subclass overrides `on_fixed_update` |
| on_awake | hook | `pass` | At `add_component` time |
| on_start | hook | `pass` | Once per `Scene.start()` |
| on_update(dt) | hook | `pass` | Every frame in Play mode |
| on_fixed_update(dt) | hook | `pass` | Every fixed step in Play mode |
| on_enable / on_disable | hook | `pass` | On entity activation / deactivation |
| on_destroy | hook | `pass` | On component/entity/scene removal |
| serialize | method | `{"type", "enabled"}` | Base dict; subclasses extend |
| gizmo hooks | methods | `gizmo_lines`, `gizmo_primitives`, `gizmo_instances`, … | Editor visualization; empty by default |

## Usage in Editor

`Add Component` menu is built from `ComponentRegistry.all()` grouped by inferred category. Inspector fields come from `_inspector_fields()` (`InspectorField` metadata). Multi-instance components show suffixed keys (`ScriptComponent.ab12cd34`) and an order control via `Entity.move_component(key, direction)`.

## Usage in Code

```python
from core.ecs import Component
from core.ecs import ComponentRegistry
from core.maths.math3d import Vec3

@ComponentRegistry.register
class Mover(Component):
    def __init__(self):
        super().__init__()
        self.speed = 5.0

    def on_update(self, dt: float):
        t = self.transform
        if t:
            t.translate(Vec3(0.0, 0.0, self.speed * dt))

    def serialize(self):
        d = super().serialize()
        d["speed"] = self.speed
        return d

    @classmethod
    def deserialize(cls, data):
        m = cls()
        m._enabled = bool(data.get("enabled", True))
        m.speed = float(data.get("speed", 5.0))
        return m
```

```python
from core.components.scripting.scriptc.component import ScriptComponent

comp.enabled = False  # leaves the scene dispatch sets until re-enabled
multi = player.add_component(ScriptComponent())  # key like "ScriptComponent.<8-hex>"
player.move_component(multi._key, -1)  # reorder multi-instance components
```

## Serialization

Base component dict: `{"type": "<ClassName>", "enabled": true, "_key": "<key>"}` (`_key` added by `Entity.serialize`). Subclasses add their fields; unknown `type` names are skipped on load (`Entity.deserialize` checks `registry.get(ctype)`).

## Related pages

- [ECS Overview](./ecs-overview.md)
- [Entity](./entity.md)
- [Registry and Serialization](./registry-serialization.md)
- [Scripting](../scripting/overview.md)
- [Script Examples](../scripting/examples.md)

## Verified against

- `core/ecs/component.py` (`__init_subclass__`, `enabled` setter, hooks, `serialize`/`deserialize`, `transform` cache)
- `core/ecs/entity.py` (`add_component` → `on_awake`, `_make_component_key`, `move_component`)
- `core/ecs/query.py` (`run_update`, `run_fixed`, `run_start`, partitioning)
- `core/ecs/registry.py` (`register`)
- `core/components/scripting/scriptc/component.py` (`_allow_multiple = True`)
