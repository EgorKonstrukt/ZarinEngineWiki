# Каталог полей инспектора

> Поля инспектора — это строки `InspectorField(name, label, FieldType, ...)` из `_inspector_fields()` плюс скриптовые аннотации типов (`scriptc/fields.py`), отображающиеся на тот же набор `FieldType`. Полный список типов — в `core/components/inspector_meta.py`.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

Два пути создания дают одни и те же типы полей. Нативные компоненты объявляют классметод `_inspector_fields()`, возвращающий строки `InspectorField` (метка, числовые лимиты, `enum_class` или `enum_options`, `file_filter`, `resource_type`, `element_fields` для списков, `toggle_field`, `readonly`, `description`, `default_value`). Скриптовые компоненты выводят поля из аннотаций класса через `ScriptFieldsMixin`: `get_type_hints(cls, include_extras=True)` плюс метаданные `Range` из `typing.Annotated`.

Числовые ручки `InspectorField` по умолчанию: `min_val=-1e17`, `max_val=1e17`, `step=0.1`, `decimals=4`. `ListElementField` несёт те же ручки на каждую колонку списка.

## Свойства

Все значения `FieldType` (`core/components/inspector_meta.py`):

| FieldType | нативный пример | скриптовая аннотация |
|---|---|---|
| `FLOAT` | `InspectorField("x", "X", FieldType.FLOAT, min_val=-10.0, max_val=100.0, step=0.5, decimals=3)` | `x: float` |
| `INT` | `InspectorField("n", "N", FieldType.INT, min_val=0, max_val=255)` | `n: int` |
| `BOOL` | `InspectorField("flag", "Flag", FieldType.BOOL)` | `flag: bool` |
| `STRING` | `InspectorField("name", "Name", FieldType.STRING)` | `name: str` |
| `TEXTAREA` | `InspectorField("text", "Text", FieldType.TEXTAREA)` | — (только натив) |
| `ENUM` | `InspectorField("mode", "Mode", FieldType.ENUM, enum_class=Mode)` или `enum_options=["Global", "Local"]` | `mode: Mode` (подкласс `Enum`) |
| `VEC2` / `VEC3` / `VEC4` | `InspectorField("center", "Center", FieldType.VEC3)` | `center: Vec3` |
| `SLIDER` | `InspectorField("k", "K", FieldType.SLIDER, min_val=0.0, max_val=1.0, step=0.01)` | `k: Annotated[float, Range(0.0, 1.0)]` |
| `INT_SLIDER` | `InspectorField("n", "N", FieldType.INT_SLIDER, min_val=0, max_val=100, step=1)` | `n: Annotated[int, Range(0, 100)]` |
| `VEC2_SLIDER` / `VEC3_SLIDER` | `InspectorField("v", "V", FieldType.VEC3_SLIDER, min_val=-1.0, max_val=1.0)` | — (только натив) |
| `LAYER` / `LAYER_MASK` | `InspectorField("layer", "Layer", FieldType.LAYER)` | — (только натив) |
| `GAMEOBJECT` (сущность) | `LIST` из `ListElementField("value", "Collider", FieldType.GAMEOBJECT)`; хранится как строка id сущности | аннотация `target: "Entity"` |
| `RESOURCE_PATH` | `InspectorField("texture_path", "Texture", FieldType.RESOURCE_PATH, file_filter="Textures (*.png *.jpg *.jpeg)")` | `path: str` + запись в `RESOURCE_TYPE_FILTERS` |
| `RESOURCE` | `InspectorField("resource_field", "Resource", FieldType.RESOURCE, file_filter="All Files (*)")` | — (только натив) |
| `ASSET` | `InspectorField("physic_material", "Physic Material", FieldType.ASSET, resource_type="physicmaterial")` | — (только натив) |
| `COLOR` | `InspectorField("color", "Color", FieldType.COLOR)`; значение — float-список вида `[1.0, 0.2, 0.1]` | — (только натив) |
| `CURVE` | `InspectorField("pull_curve", "Pull Curve", FieldType.CURVE)`; значение — `Curve` | `curve: Curve` |
| `GRADIENT` | `InspectorField("gradient_field", "Gradient", FieldType.GRADIENT)`; значение — `[(t, [r,g,b,a]), ...]` | — (только натив) |
| `SPLIT_GRADIENT` | член enum `FieldType` | — (только натив) |
| `KEYBINDING` | `InspectorField("unlock_key", "Unlock Key", FieldType.KEYBINDING)`; хранится строкой вида `"Space"` | — (только натив) |
| `ANCHOR` | `InspectorField("anchor_field", "Anchor", FieldType.ANCHOR)`; хранится int | — (только натив) |
| `LIST` | `InspectorField("points", "Points", FieldType.LIST, element_fields=[ListElementField("point", "Point", FieldType.VEC3)])` | — (только натив) |
| `BUTTON` | `InspectorField("method_name", "Label", FieldType.BUTTON)`; инспектор вызывает метод | скриптовый список `_inspector_buttons = [(method, label)]` |
| `HEADER` | `InspectorField("", "Shape", FieldType.HEADER)` — разделитель секций | авто-заголовок `"Script Variables"` |
| `LAYER`, `LAYER_MASK` | физика `layer` / `mask` (`BoxCollider`: `int`, дефолт маски `0xFFFF`) | — |

Скриптовые дополнения (`core/components/scripting/scriptc/fields.py`): `Range(min_value, max_value, step=None)` внутри `Annotated` (простой список/кортеж `[min, max]` / `[min, max, step]` тоже подходит); `RESOURCE_TYPE_FILTERS` отображает имена ресурсов (`mesh`, `material`, `texture`, `audio`, `script`, `prefab`, `scene`, `animclip`, `animcontroller`, `physicmaterial`) на фильтры файлового диалога; обычные неаннотированные атрибуты класса `int/float/bool/str` тоже становятся полями; неизвестные типы аннотаций откатываются на `FieldType.FLOAT`.

## Использование в редакторе

Инспектор строит по виджету на каждый `InspectorField`; строки `HEADER` делят секции; строки `BUTTON` вызывают именованный метод; строки `LIST` открывают табличный редактор по `element_fields`. Значения скриптовых переменных живут в `ScriptComponent._field_values` и при применении переносятся на инстанс скрипта (списки `Vec2/3/4` становятся векторами, строки id сущностей резолвятся через сцену, значения `Enum` приводятся к типу).

## Использование в коде

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

## Сериализация

Значения полей сериализуются как обычные записи dict компонента (`Vec3` → `to_list()`, `Curve` → `to_dict()`, цвета/градиенты → вложенные float-списки). `ScriptComponent` держит их в своём dict скриптовых полей; id сущностей сохраняются строками и пере-резолвятся при загрузке.

## Связанные страницы

- [Создание компонентов](./component-authoring.md)
- [Компоненты и жизненный цикл](../components-lifecycle.md)
- [Скриптинг: поля инспектора и Range](../../scripting/inspector-fields.md)
- [Компонент ScriptComponent](../../scripting/script-component.md)
- [Curve API](./curve-api.md)
- [Инспектор](../../editor/inspector.md)

## Проверено по

- `core/components/inspector_meta.py` (`FieldType`, `ListElementField`, `InspectorField`)
- `core/components/test_inspector_fields.py` (по одному каждого нативного типа полей)
- `core/components/physics/box_collider.py` (`LAYER`, `LAYER_MASK`, `VEC3`, `BOOL`, `ASSET`)
- `core/components/physics/physbone/params.py` (`CURVE`, `LIST` из `GAMEOBJECT`, `ENUM`)
- `core/components/rendering/renderers/text_renderer.py` (`TEXTAREA`, `COLOR` с `toggle_field`, `RESOURCE_PATH`)
- `core/components/scripting/scriptc/fields.py` (`Range`, `RESOURCE_TYPE_FILTERS`, `PY_TYPE_TO_FIELD`, `_py_type_to_field_type`, `_build_fields_from_class`)
- `core/components/scripting/scriptc/component.py` (ресурсное поле `script_path`)
- `editor/inspector/panelp/sections.py` (`live_inspector_fields`)
