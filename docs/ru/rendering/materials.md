# Материалы
> Ассет материала: `.mat` JSON (`core/assets/matp/materials.py:14-169`), резолвит/кэширует `MaterialManager` (`core/renderer/mats/manager.py:54-80`).
[Home](../../index.md) · [Section index](./_section.md)
## Overview
`Material { name, shader_path, properties }`. `shader_path = "default"` — встроенный PBR; иначе поиск от корня проекта, затем `core/shaders/` движка (`materials.py:26-46`). Отсутствующие файлы варнят один раз и откатываются на дефолт (`manager.py:63-71`). `MeshRenderer.materials[i].path` указывает на `.mat`.
## Properties
| name | type | default | description |
|---|---|---|---|
| `name` | string | `""` | Имя материала (`materials.py:14-24`). |
| `shader_path` | string | `"default"` | Путь `.shader` или `default` (`materials.py:48-52,110-118`). |
| `properties` | object | `{}` | Юниформы: `_BaseColor`, `_Metallic`, `_Smoothness` (`manager.py:89-96`). |
## Usage in Editor
Создать `.mat` в Assets, выбрать Shader, править открытые свойства. Назначать в слоты [Mesh Renderer](./mesh-renderer.md).
## Usage in Code
```python
from core.assets.matp import Material
m = Material.load("assets/materials/Brick.mat", project_root)
```
## Material/Shader snippet
`assets/materials/ProBuilderPrototype.mat` (дословно):
```json
{"name": "ProBuilderPrototype", "properties": {"_BaseColor": [1.0, 1.0, 1.0, 1.0], "_EmissionColor": [0.0, 0.0, 0.0, 0.0], "_EmissionIntensity": 0.0, "_Metallic": 0.0, "_Smoothness": 0.5, "albedo_texture": "../textures/prototype_texture.png"}, "shader_path": "default"}
```
## Скриншоты

![Прототипная текстура (WALL, 1x1 метр, 1024x1024)](../../assets/screenshots/prototype-texture.png)

> `prototype_texture.png`, используемая в `assets/materials/ProBuilderPrototype.mat`
> как `albedo_texture`. Файл: `assets/screenshots/prototype-texture.png`
> (проверенная копия `prototype_texture.png` из репозитория движка; идентична
> `assets/textures/prototype_texture.png`).

## Related pages
- [Shaders](./shaders.md), [Mesh Renderer](./mesh-renderer.md), [Pipeline](./pipeline.md)
## Verified against
- `core/assets/matp/materials.py:14-169`
- `core/renderer/mats/manager.py:54-100`
- `assets/materials/ProBuilderPrototype.mat`
