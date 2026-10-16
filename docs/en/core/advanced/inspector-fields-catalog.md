# Inspector Fields Catalog

> Inspector fields are `InspectorField(name, label, FieldType, ...)` rows returned by `_inspector_fields()`, plus script-side type annotations (`scriptc/fields.py`) that map to the same `FieldType` set. The full type list lives in `core/components/inspector_meta.py`.

[Home](../../../index.md) | [Section index](./_section.md)

## Overview

Two authoring paths produce the same field types. Native components declare a `_inspector_fields()` classmethod returning `InspectorField` rows (label, numeric limits, `enum_class` or `enum_options`, `file_filter`, `resource_type`, `element_fields` for lists, `toggle_field`, `readonly`, `description`, `default_value`). Script components derive fields from Python class annotations via `ScriptFieldsMixin`: `get_type_hints(cls, include_extras=True)` plus `Range` metadata from `typing.Annotated`.

`InspectorField` numeric knobs default to `min_val=-1e17`, `max_val=1e17`, `step=0.1`, `decimals=4`. `ListElementField` carries the same knobs per list column.

## Properties

All `FieldType` values (`core/components/inspector_meta.py`):

| FieldType | native example | script annotation |
|---|---|---|
| `FLOAT` | `InspectorField("x", "X", FieldType.FLOAT, min_val=-10.0, max_val=100.0, step=0.5, decimals=3)` | `x: float` |
| `INT` | `InspectorField("n", "N", FieldType.INT, min_val=0, max_val=255)` | `n: int` |
| `BOOL` | `InspectorField("flag", "Flag", FieldType.BOOL)` | `flag: bool` |
| `STRING` | `InspectorField("name", "Name", FieldType.STRING)` | `name: str` |
| `TEXTAREA` | `InspectorField("text", "Text", FieldType.TEXTAREA)` | — (native only) |
| `ENUM` | `InspectorField("mode", "Mode", FieldType.ENUM, enum_class=Mode)` or `enum_options=["Global", "Local"]` | `mode: Mode` (an `Enum` subclass) |
| `VEC2` / `VEC3` / `VEC4` | `InspectorField("center", "Center", FieldType.VEC3)` | `center: Vec3` |
| `SLIDER` | `InspectorField("k", "K", FieldType.SLIDER, min_val=0.0, max_val=1.0, step=0.01)` | `k: Annotated[float, Range(0.0, 1.0)]` |
| `INT_SLIDER` | `InspectorField("n", "N", FieldType.INT_SLIDER, min_val=0, max_val=100, step=1)` | `n: Annotated[int, Range(0, 100)]` |
| `VEC2_SLIDER` / `VEC3_SLIDER` | `InspectorField("v", "V", FieldType.VEC3_SLIDER, min_val=-1.0, max_val=1.0)` | — (native only) |
| `LAYER` / `LAYER_MASK` | `InspectorField("layer", "Layer", FieldType.LAYER)` | — (native only) |
| `GAMEOBJECT` (entity) | `LIST` of `ListElementField("value", "Collider", FieldType.GAMEOBJECT)`; stored as entity id string | `target: "Entity"` annotation |
| `RESOURCE_PATH` | `InspectorField("texture_path", "Texture", FieldType.RESOURCE_PATH, file_filter="Textures (*.png *.jpg *.jpeg)")` | `path: str` + `RESOURCE_TYPE_FILTERS` entry |
| `RESOURCE` | `InspectorField("resource_field", "Resource", FieldType.RESOURCE, file_filter="All Files (*)")` | — (native only) |
| `ASSET` | `InspectorField("physic_material", "Physic Material", FieldType.ASSET, resource_type="physicmaterial")` | — (native only) |
| `COLOR` | `InspectorField("color", "Color", FieldType.COLOR)`; value is a float list like `[1.0, 0.2, 0.1]` | — (native only) |
| `CURVE` | `InspectorField("pull_curve", "Pull Curve", FieldType.CURVE)`; value is a `Curve` | `curve: Curve` |
| `GRADIENT` | `InspectorField("gradient_field", "Gradient", FieldType.GRADIENT)`; value is `[(t, [r,g,b,a]), ...]` | — (native only) |
| `SPLIT_GRADIENT` | `FieldType` enum member | — (native only) |
| `KEYBINDING` | `InspectorField("unlock_key", "Unlock Key", FieldType.KEYBINDING)`; stored as string like `"Space"` | — (native only) |
| `ANCHOR` | `InspectorField("anchor_field", "Anchor", FieldType.ANCHOR)`; stored as int | — (native only) |
| `LIST` | `InspectorField("points", "Points", FieldType.LIST, element_fields=[ListElementField("point", "Point", FieldType.VEC3)])` | — (native only) |
| `BUTTON` | `InspectorField("method_name", "Label", FieldType.BUTTON)`; inspector calls the method | script `_inspector_buttons = [(method, label)]` list |
| `HEADER` | `InspectorField("", "Shape", FieldType.HEADER)` — section separator | auto-inserted `"Script Variables"` header |
| `LAYER`, `LAYER_MASK` | physics `layer` / `mask` (`BoxCollider`: `int`, mask default `0xFFFF`) | — |

Script-side extras (`core/components/scripting/scriptc/fields.py`): `Range(min_value, max_value, step=None)` inside `Annotated` (a plain `[min, max]` / `[min, max, step]` list/tuple works too); `RESOURCE_TYPE_FILTERS` maps resource names (`mesh`, `material`, `texture`, `audio`, `script`, `prefab`, `scene`, `animclip`, `animcontroller`, `physicmaterial`) to file-dialog filters; unannotated plain `int/float/bool/str` class attributes also become fields; unknown annotation types fall back to `FieldType.FLOAT`.

## Usage in Editor

The inspector builds one widget row per `InspectorField`; `HEADER` rows split sections; `BUTTON` rows invoke the named method; `LIST` rows open a table editor driven by `element_fields`. Script variable values live in `ScriptComponent._field_values` and are pushed onto the script instance on apply (`Vec2/3/4` lists become vectors, entity-id strings resolve via the scene, `Enum` values are coerced).

## Usage in Code

```python
from core.components.inspector_meta import FieldType, InspectorField, ListElementField
from core.foundation.curve import Curve

@classmethod
def _inspector_fields(cls) -> list[InspectorField]:
    return [
        InspectorField("", "Shape", FieldType.HEADER),
        InspectorField("size", "Size", FieldType.VEC3),
        InspectorField("mode", "Mode", FieldType.ENUM, enum_options=["Infinite", "Box"]),
        InspectorField("tint", "Tint", FieldType.COLOR),
        InspectorField("falloff", "Falloff", FieldType.CURVE),
        InspectorField("icon", "Icon", FieldType.RESOURCE_PATH,
                       file_filter="Textures (*.png *.jpg *.jpeg)"),
        InspectorField("targets", "Targets", FieldType.LIST,
                       element_fields=[ListElementField("value", "Target", FieldType.GAMEOBJECT)]),
    ]
```

```python
from typing import Annotated
from core.components.scripting.scriptc.fields import Range
from core.foundation.curve import Curve
from core.maths.math3d import Vec3

class Mover:
    speed: Annotated[float, Range(0.0, 20.0)] = 5.0
    count: Annotated[int, Range(1, 10)] = 3
    offset: Vec3 = Vec3(0, 0, 0)
    falloff: Curve = Curve()
    target: "Entity" = None
    _inspector_buttons = [("reset", "Reset")]
```

## Serialization

Field values serialize as plain component dict entries (`Vec3` → `to_list()`, `Curve` → `to_dict()`, colors/gradients → nested float lists). `ScriptComponent` keeps them under its script-fields dict; entity ids persist as strings and re-resolve on load.

## Related pages

- [Component Authoring](./component-authoring.md)
- [Components and Lifecycle](../components-lifecycle.md)
- [Scripting: Inspector Fields and Range](../../scripting/inspector-fields.md)
- [ScriptComponent](../../scripting/script-component.md)
- [Curve API](./curve-api.md)
- [Inspector](../../editor/inspector.md)

## Verified against

- `core/components/inspector_meta.py` (`FieldType`, `ListElementField`, `InspectorField`)
- `core/components/test_inspector_fields.py` (one of every native field type)
- `core/components/physics/box_collider.py` (`LAYER`, `LAYER_MASK`, `VEC3`, `BOOL`, `ASSET`)
- `core/components/physics/physbone/params.py` (`CURVE`, `LIST` of `GAMEOBJECT`, `ENUM`)
- `core/components/rendering/renderers/text_renderer.py` (`TEXTAREA`, `COLOR` with `toggle_field`, `RESOURCE_PATH`)
- `core/components/scripting/scriptc/fields.py` (`Range`, `RESOURCE_TYPE_FILTERS`, `PY_TYPE_TO_FIELD`, `_py_type_to_field_type`, `_build_fields_from_class`)
- `core/components/scripting/scriptc/component.py` (`script_path` resource field)
- `editor/inspector/panelp/sections.py` (`live_inspector_fields`)
