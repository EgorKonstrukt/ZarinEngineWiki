# Библиотека материалов

> Все поставляемые ассеты материалов в `assets/materials/` (на данный момент один `.mat`-файл), таблица полей `.mat`-JSON из `core/assets/matp/materials.py` и дословный пример.

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Ассеты материалов — это `.mat`-JSON (`Material { name, shader_path, properties }`), загружает класс `Material` (`core/assets/matp/materials.py:14-169`), кэширует `MaterialManager` (`core/renderer/mats/manager.py:54-80`). `shader_path = "default"` — встроенный PBR; любой другой путь резолвится сначала относительно корня проекта, затем относительно движкового `core/shaders/` (`materials.py:26-46`). Отсутствующие файлы один раз варнят и откатываются на дефолт (`manager.py:63-71`). Файлов `.zpem` в `assets/` на момент написания нет.

## Listing (`assets/materials/`)

| file | material | shader | purpose |
|---|---|---|---|
| `ProBuilderPrototype.mat` | `ProBuilderPrototype` | `default` (встроенный PBR) | Серая прототипная сетка для блок-аута геометрии (`albedo_texture: ../textures/prototype_texture.png`). |

## Поля `.mat`-JSON

| field | type | default | description |
|---|---|---|---|
| `name` | string | `""` | Имя материала (`materials.py:14-24`). |
| `shader_path` | string | `"default"` | Путь к `.shader` или `"default"` под встроенный PBR (`materials.py:48-52,110-118`). Не-`.shader` расширения отклоняются при загрузке свойств шейдера. |
| `properties` | object | `{}` | Юниформы шейдера (`manager.py:89-96`). Недостающие ключи добиваются из дефолтов `Properties` шейдера (`materials.py:63-66`). |

Порядок резолва не-дефолтного пути (`materials.py:26-46`): абсолютный путь если существует → `<project_root>/<path>` → `<engine_root>/<path>` → `<engine_root>/core/shaders/<path>` → поиск basename в `materials/`, `internal/`, `compute/`, `include/`, `legacy/`.

## Пример (verbatim)

`assets/materials/ProBuilderPrototype.mat`:

```json
{"name": "ProBuilderPrototype", "properties": {"_BaseColor": [1.0, 1.0, 1.0, 1.0], "_EmissionColor": [0.0, 0.0, 0.0, 0.0], "_EmissionIntensity": 0.0, "_Metallic": 0.0, "_Smoothness": 0.5, "albedo_texture": "../textures/prototype_texture.png"}, "shader_path": "default"}
```

## Usage in Editor

Создайте `.mat` в Assets, выберите Shader, отредактируйте открытые `Properties`. Назначьте файл в слоты материалов [Mesh Renderer](../mesh-renderer.md).

## Usage in Code

```python
from core.assets.matp import Material
m = Material.load("assets/materials/ProBuilderPrototype.mat", project_root)
m.set_property("_Metallic", 0.2)
m.save("assets/materials/ProBuilderPrototype.mat", project_root)
```

## Related pages

- [Материалы](../materials.md), [Шейдеры](../shaders.md), [Каталог шейдеров](./shader-catalog.md), [Mesh Renderer](../mesh-renderer.md)

## Verified against

- `assets/materials/ProBuilderPrototype.mat` (verbatim выше; единственный `.mat` в `assets/`; файлов `.zpem` в `assets/` нет)
- `core/assets/matp/materials.py:14-70` (`Material`, `_resolve_shader_path`, `load_shader_properties`, `save`)
- `core/renderer/mats/manager.py:54-100`
