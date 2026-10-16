# Создание компонентов

> Кастомный компонент — это подкласс `Component` с декоратором `@ComponentRegistry.register` (обязательный гейт), парой `serialize`/`deserialize`, опциональным классметодом `_inspector_fields()` и хуками жизненного цикла. Регистрация также определяет категорию в меню Add Component по пути модуля `core.components.<sub>`.

[Home](../../../index.md) | [Section index](./_section.md)

## Обзор

Каждый компонент обязан быть зарегистрирован: `ComponentRegistry.register` сохраняет класс по имени в `_registry` и выводит категорию редактора из пути модуля — часть после `components` в строке вида `core.components.physics.box_collider` (см. таблицу ниже). Архитектурный гейт (`tools/arch_gate.py`, `check_register`) бракует любой незарегистрированный подкласс `Component`, кроме имён с `Mixin`/`Base`, имён на `_` и allow-листа `{"SkyIbl", "RaytraceComponent"}`.

Хуки жизненного цикла (по умолчанию no-op): `on_awake` выполняется внутри `Entity.add_component`; `on_start` — один раз за `Scene.start()` (старт Play); `on_update(dt)` / `on_fixed_update(dt)` — каждый кадр / фиксированный шаг, пока сущность активна и компонент `enabled`; `on_enable` / `on_disable` отслеживают активацию; `on_destroy` — при удалении. `__init_subclass__` выставляет `_updates` / `_fixed_updates` автоматически в зависимости от того, переопределил ли подкласс `on_update` / `on_fixed_update`.

Персистентность — пара `serialize`/`deserialize`: база возвращает `{"type": "<ClassName>", "enabled": ...}`, подкласс дописывает свои поля; `deserialize` — классметод, пересоздающий объект попольно через `data.get(...)` с дефолтами. Неизвестные имена `type` при загрузке пропускаются.

Мульти-инстанс компоненты ставят `_allow_multiple = True` (например `BoxCollider`, `ScriptComponent`); их ключи в сущности — `Class.<8-hex>`, а инспектор показывает контрол порядка (`Entity.move_component`).

## Свойства

| имя | тип | дефолт | описание |
|---|---|---|---|
| `ComponentRegistry.register` | декоратор | — | Обязателен: регистрирует имя класса, выводит категорию |
| `_inspector_fields()` | классметод | не определён | Возвращает `list[InspectorField]` для инспектора |
| `live_inspector_fields()` | хук | `()` | Дополнительные runtime-поля, добавляемые инспектором |
| `serialize` / `deserialize` | методы | базовый dict | Подкласс расширяет dict / пересоздаёт через `data.get` с дефолтами |
| `_allow_multiple` | bool | `False` | `True` → ключ `Class.<8-hex>`, несколько штук на сущность |
| `on_awake` | хук | `pass` | В момент `add_component` (даже в edit-режиме) |
| `on_start` | хук | `pass` | Один раз за `Scene.start()` |
| `on_update(dt)` / `on_fixed_update(dt)` | хуки | `pass` | Каждый кадр / фиксированный шаг в Play-режиме |
| `on_enable` / `on_disable` / `on_destroy` | хуки | `pass` | Активация / деактивация / удаление |
| `transform` | property | кэшируется | `Transform` владеющей сущности, кэшируется на компонент |

Вывод категории (`ComponentRegistry._infer_category` + `_category_name_map`):

| модуль `core.components.<sub>...` | категория меню |
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
| всё остальное | `<Sub>` с заглавной буквы |

## Использование в редакторе

Компонент появляется в меню Add Component под своей выведенной категорией. Строки `_inspector_fields()` рисуются как виджеты инспектора; мульти-копии получают суффиксные ключи и контрол порядка. Незарегистрированные классы в меню не попадают и валят гейт `register`.

## Использование в коде

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
entity.add_component(Health())  # on_awake выполняется здесь
```

## Сериализация

Базовый dict `{"type": "Health", "enabled": true}` плюс поля подкласса (`max_hp`, `regen`). `Entity.deserialize` ищет каждый `type` в реестре и пропускает неизвестные имена, поэтому переименование зарегистрированного класса ломает старые сцены.

## Связанные страницы

- [Обзор ECS](../ecs-overview.md)
- [Компоненты и жизненный цикл](../components-lifecycle.md)
- [Реестр и сериализация](../registry-serialization.md)
- [Сущность Entity](../entity.md)
- [Каталог полей инспектора](./inspector-fields-catalog.md)
- [Скриптинг: поля инспектора и Range](../../scripting/inspector-fields.md)

## Проверено по

- `core/ecs/component.py` (`__init_subclass__`, хуки, сеттер `enabled`, `serialize`/`deserialize`, кэш `transform`, `live_inspector_fields`)
- `core/ecs/registry.py` (`register`, `_infer_category`, `_category_name_map`)
- `core/ecs/entity.py` (`add_component` → `on_awake`, `_make_component_key`)
- `core/ecs/query.py` (`Scene.start` → `on_start`)
- `core/components/physics/box_collider.py` (`@ComponentRegistry.register`, `_allow_multiple`, `_inspector_fields`, `serialize`/`deserialize`)
- `core/components/test_inspector_fields.py` (компонент-пример всех типов полей)
- `tools/arch_gate.py` (`check_register`, `ALLOW_UNREGISTERED`)
