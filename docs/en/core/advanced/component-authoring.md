# Component Authoring

> A custom component is a `Component` subclass decorated with `@ComponentRegistry.register` (mandatory gate), with a `serialize`/`deserialize` pair, an optional `_inspector_fields()` classmethod, and lifecycle hooks. Registration also decides the Add Component menu category from the module path `core.components.<sub>`.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

Every component must be registered: `ComponentRegistry.register` stores the class by name in `_registry` and infers its editor category from the module path — the part after `components` in e.g. `core.components.physics.box_collider` (see table below). The architecture gate (`tools/arch_gate.py`, `check_register`) fails any `Component` subclass that is not registered, except names containing `Mixin`/`Base`, names starting with `_`, and the allow-list `{"SkyIbl", "RaytraceComponent"}`.

Lifecycle hooks (all no-ops by default): `on_awake` runs inside `Entity.add_component`; `on_start` runs once per `Scene.start()` (Play start); `on_update(dt)` / `on_fixed_update(dt)` run every frame / fixed step while the entity is active and the component is `enabled`; `on_enable` / `on_disable` track activation; `on_destroy` runs on removal. `__init_subclass__` sets `_updates` / `_fixed_updates` automatically depending on whether the subclass overrides `on_update` / `on_fixed_update`.

Persistence is a `serialize`/`deserialize` pair: the base returns `{"type": "<ClassName>", "enabled": ...}` and each subclass adds its own fields; `deserialize` is a classmethod that rebuilds the object field by field with `data.get(...)` defaults. Unknown `type` names are skipped on load.

Multi-instance components set `_allow_multiple = True` (e.g. `BoxCollider`, `ScriptComponent`); their entity keys become `Class.<8-hex>` and the inspector shows an order control (`Entity.move_component`).

## Properties

| name | type | default | description |
|---|---|---|---|
| `ComponentRegistry.register` | decorator | — | Mandatory: registers class name, infers category |
| `_inspector_fields()` | classmethod | not defined | Returns `list[InspectorField]` for the inspector |
| `live_inspector_fields()` | hook | `()` | Extra runtime-driven fields appended by the inspector |
| `serialize` / `deserialize` | methods | base dict | Subclass extends dict / rebuilds via `data.get` defaults |
| `_allow_multiple` | bool | `False` | `True` → key `Class.<8-hex>`, several per entity |
| `on_awake` | hook | `pass` | At `add_component` time (even in edit mode) |
| `on_start` | hook | `pass` | Once per `Scene.start()` |
| `on_update(dt)` / `on_fixed_update(dt)` | hooks | `pass` | Per frame / fixed step in Play mode |
| `on_enable` / `on_disable` / `on_destroy` | hooks | `pass` | Activation / deactivation / removal |
| `transform` | property | cached | Owning entity's `Transform`, cached per component |

Category inference (`ComponentRegistry._infer_category` + `_category_name_map`):

| module `core.components.<sub>...` | menu category |
|---|---|
| `transform` | Transform |
| `rendering` | Rendering |
| `physics` | Physics |
| `physics2d` | Physics 2D |
| `lighting` | Lighting |
| `audio` | Audio |
| `constraints` | Constraints |
| `network` | Network |
| `scripting` | Scripting |
| anything else | `<Sub>` capitalized |

## Usage in Editor

The component appears in the Add Component menu under its inferred category. Its `_inspector_fields()` rows render as inspector widgets; multi-instance copies get suffixed keys and an order control. Unregistered classes never appear in the menu and fail the `register` gate.

## Usage in Code

```python
from core.ecs import Component, ComponentRegistry
from core.components.inspector_meta import FieldType, InspectorField
from core.maths.math3d import Vec3


@ComponentRegistry.register
class Health(Component):
    _allow_multiple = False

    @classmethod
    def _inspector_fields(cls) -> list[InspectorField]:
        return [
            InspectorField("max_hp", "Max HP", FieldType.FLOAT, min_val=1.0, max_val=1000.0),
            InspectorField("regen", "Regen", FieldType.BOOL),
        ]

    def __init__(self):
        super().__init__()
        self.max_hp: float = 100.0
        self.regen: bool = False

    def on_update(self, dt: float):
        if self.regen:
            pass

    def serialize(self) -> dict:
        d = super().serialize()
        d.update({"max_hp": self.max_hp, "regen": self.regen})
        return d

    @classmethod
    def deserialize(cls, data: dict) -> "Health":
        h = cls()
        h._enabled = bool(data.get("enabled", True))
        h.max_hp = float(data.get("max_hp", 100.0))
        h.regen = bool(data.get("regen", False))
        return h
```

```python
entity.add_component(Health())  # on_awake runs here
```

## Serialization

Base dict `{"type": "Health", "enabled": true}` plus subclass fields (`max_hp`, `regen`). `Entity.deserialize` looks each `type` up in the registry and skips unknown names, so renaming a registered class breaks old scenes.

## Related pages

- [ECS Overview](../ecs-overview.md)
- [Components and Lifecycle](../components-lifecycle.md)
- [Registry and Serialization](../registry-serialization.md)
- [Entity](../entity.md)
- [Inspector Fields Catalog](./inspector-fields-catalog.md)
- [Scripting: Inspector Fields and Range](../../scripting/inspector-fields.md)

## Verified against

- `core/ecs/component.py` (`__init_subclass__`, hooks, `enabled` setter, `serialize`/`deserialize`, `transform` cache, `live_inspector_fields`)
- `core/ecs/registry.py` (`register`, `_infer_category`, `_category_name_map`)
- `core/ecs/entity.py` (`add_component` → `on_awake`, `_make_component_key`)
- `core/ecs/query.py` (`Scene.start` → `on_start`)
- `core/components/physics/box_collider.py` (`@ComponentRegistry.register`, `_allow_multiple`, `_inspector_fields`, `serialize`/`deserialize`)
- `core/components/test_inspector_fields.py` (full field-type example component)
- `tools/arch_gate.py` (`check_register`, `ALLOW_UNREGISTERED`)
