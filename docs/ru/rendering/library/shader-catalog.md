# Каталог шейдеров

> Полный листинг всех исходников шейдеров в `core/shaders/` (`materials/`, `internal/`, `compute/`, `legacy/`, `include/`): по одной строке назначения на файл и дословные (verbatim) `Properties`-блоки главных шейдеров (PBR, Default, Sky, Water, Clouds).

[Home](../../../index.md) · [Section index](../_section.md)

## Overview

Исходники шейдеров лежат в `core/shaders/` и компилируются `ShaderManager` (`core/renderer/shaderp/manager.py`) с фолбэком (`shaderp/fallback.py:110`). На момент написания: `materials/` — 9 файлов, `internal/` — 22 файла, `compute/` — 11 файлов, `legacy/` — 7 файлов, `include/` — 2 файла. Материальные шейдеры объявляют `Shader "Zarin/<Name>"` с блоком `Properties`; compute-шейдеры — `ComputeShader "Zarin/<Name>"`.

## Materials (`core/shaders/materials/`)

| file | shader name | purpose |
|---|---|---|
| `PBR.shader` | `Zarin/PBR` | Дефолтный физически корректный opaque-материал (metallic/smoothness, эмиссия, normal/occlusion/height, clear-coat, анизотропия, detail-карты, subsurface, thin-film, transmission). |
| `Unlit.shader` | `Zarin/Unlit` | Неосвещённый материал без вычисления освещения. |
| `Sky.shader` | `Zarin/Sky` | Процедурный небесный купол (солнечный диск, ночное небо, звёзды, Млечный путь, луна). |
| `Skybox.shader` | `Zarin/Skybox` | Скайбокс по кубмапе. |
| `Clouds.shader` | `Zarin/Clouds` | Полноэкранные облака в духе волюметрии (покрытие, плотность, рассеяние, silver lining). |
| `CloudLayer.shader` | `Zarin/CloudLayer` | Плоский вариант облачного слоя. |
| `Water.shader` | `Zarin/Water` | Дефолтный материал воды и референс для кастомных водных шейдеров (`RenderType Transparent`). |
| `WaterSim.shader` | `Zarin/WaterSim` | Симуляционный вариант воды. |
| `Tree.shader` | `Zarin/Tree` | Материал растительности/деревьев (покачивание через эффект Tree Wind). |

## Internal (`core/shaders/internal/`)

| file | shader name | purpose |
|---|---|---|
| `Default.shader` | `Zarin/Default` | Фолбэк-материал движка (пустой `Properties`, вершинные входы инстансинга + скиннинга). |
| `Sprite.shader` | `Zarin/Sprite` | Рендер 2D-спрайтов (квады). |
| `Video.shader` | `Zarin/Video` | Рендер видео-квадов (`VideoRenderer`). |
| `Text.shader` | `Zarin/Text` | Рендер текста по SDF-квадам (`TextRenderer`). |
| `Particle.shader` | `Zarin/Particle` | CPU-билборды частиц. |
| `ParticleGpu.shader` | `Zarin/ParticleGpu` | GPU-билборды частиц. |
| `GaussianSplat.shader` | `Zarin/GaussianSplat` | Растеризация гауссовых сплатов (нужен OpenGL 4.3+). |
| `Shadow.shader` | `Zarin/Shadow` | Проход глубины карт теней. |
| `ShadowDebug.shader` | `Zarin/ShadowDebug` | Отладочная визуализация карт теней. |
| `ShadowOverlay.shader` | `Zarin/ShadowOverlay` | Композитинг теневого оверлея. |
| `Projector.shader` | `Zarin/Projector` | Световой проектор (компонент `Projector`). |
| `Outline.shader` | `Zarin/Outline` | Обводка выделения (wireframe-оверлей). |
| `ObjectFx.shader` | `Zarin/ObjectFx` | Точка инъекции объектных эффектов (связки dissolve/hologram и т.д.). |
| `Icon.shader` | `Zarin/Icon` | Редакторные билборд-иконки. |
| `Gizmo.shader` | `Zarin/Gizmo` | Редакторные линии гизмо. |
| `GizmoSolid.shader` | `Zarin/GizmoSolid` | Редакторные сплошные меши гизмо. |
| `GizmoInstLine.shader` | `Zarin/GizmoInstLine` | Инстансированные линии гизмо. |
| `Grid.shader` | `Zarin/Grid` | Редакторная сетка земли. |
| `GridOverlay.shader` | `Zarin/GridOverlay` | Композитинг оверлея сетки. |
| `SkyboxLegacy.shader` | `Zarin/SkyboxLegacy` | Легаси-путь скайбокса. |
| `Caustics.shader` | `Zarin/Caustics` | Оверлей водной каустики. |
| `Underwater.shader` | `Zarin/Underwater` | Полноэкранный подводный тинт/туман. |

## Compute (`core/shaders/compute/`)

| file | shader name | purpose |
|---|---|---|
| `Raytracing.compute` | `Zarin/Raytracing` | Ядро паттрейсинга (`MAX_INSTANCES 256`, `MAX_LIGHTS 8`). |
| `SkyEnv.compute` | `Zarin/SkyEnv` | Запекание карты окружения неба. |
| `Atmosphere.compute` | `Zarin/Atmosphere` | Запекание атмосферного рассеяния. |
| `RadianceCascades.compute` | `Zarin/RadianceCascades` | GI-проход radiance-каскадов. |
| `Cull.compute` | `Zarin/Cull` | GPU-каллинг (frustum/occlusion). |
| `BVHDebug.compute` | `Zarin/BVHDebug` | Отладочная визуализация BVH. |
| `particle.compute` | `Zarin/ParticleSim` | GPU-симуляция частиц. |
| `Tree.compute` | `Zarin/TreeLeafSim` | Симуляция листвы деревьев. |
| `terrain_gen.compute` | `TerrainGenerator` | Процедурная генерация террейна. |
| `shadow_cascade.compute` | — | Хелпер каскадных теней (без заголовка `ComputeShader`, только GLSL-вход). |
| `NavPathfinding.compute` | `Zarin/NavPathfinding` | GPU-поиск пути по навмешу. |

## Legacy (`core/shaders/legacy/`) и инклуды (`core/shaders/include/`)

| file | purpose |
|---|---|
| `legacy/voxelize.comp` | Легаси compute-вход вокселизации. |
| `legacy/transform.comp` | Легаси compute-вход трансформов. |
| `legacy/skinning.comp` | Легаси compute-вход скиннинга. |
| `legacy/cull.comp` | Легаси compute-вход каллинга. |
| `legacy/aabb.comp` | Легаси compute-вход AABB. |
| `legacy/Tree.gshader` | Легаси geometry-шейдер деревьев. |
| `legacy/object_fx.geom` | Легаси geometry-шейдер объектных эффектов. |
| `include/caustics.glsl` | Общий GLSL-чанк каустики. |
| `include/area_shadows.glsl` | Общий GLSL-чанк area-теней. |

## Ключевые Properties-блоки (verbatim)

`core/shaders/materials/PBR.shader:9-16` (начало):

```c
Properties
{
    [MainColor] _BaseColor("Base Color", Color) = (1, 1, 1, 1)
    [MainTexture] _BaseMap("Base Map", 2D) = "white" {}
    _Metallic("Metallic", Range(0, 1)) = 0
    _Smoothness("Smoothness", Range(0, 1)) = 0.5
    _EmissionColor("Emission Color", Color) = (0, 0, 0, 0)
    _EmissionIntensity("Emission Intensity", Float) = 0
```

`core/shaders/internal/Default.shader:9-11` (блок целиком):

```c
Properties
{
}
```

`core/shaders/materials/Sky.shader:9-16` (начало):

```c
Properties
{
    _SunDirection("Sun Direction", Vector) = (0, -0.3, -1, 0)
    _SunColor("Sun Color", Color) = (1, 0.95, 0.85, 1)
    _SunIntensity("Sun Intensity", Float) = 1
    _SunAngularRadius("Sun Angular Radius (deg)", Float) = 0.27
    _SunLimbDarkening("Sun Limb Darkening", Range(0, 1)) = 0.7
    _SunConvergence("Sun Edge Softness", Range(0, 1)) = 0.5
```

`core/shaders/materials/Water.shader:68-75` (начало):

```c
Properties
{
    _DeepColor("Deep Color", Color) = (0.015, 0.13, 0.22, 1)
    _ShallowColor("Shallow Color", Color) = (0.13, 0.46, 0.52, 1)
    _FoamColor("Foam Color", Color) = (0.92, 0.97, 1.0, 1)
    _SSSColor("SSS Color", Color) = (0.0, 0.55, 0.45, 1)
    _HorizonColor("Horizon Color", Color) = (0.62, 0.78, 0.86, 1)
    _Smoothness("Smoothness", Float) = 0.92
```

`core/shaders/materials/Clouds.shader:9-16` (начало):

```c
Properties
{
    _Coverage("Cloud Coverage", Range(0, 1)) = 0.5
    _Density("Cloud Density", Float) = 1.0
    _Speed("Wind Speed", Float) = 0.1
    _WindDirection("Wind Direction", Float) = 18.0
    _Height("Cloud Height", Float) = 50.0
    _Thickness("Cloud Thickness", Float) = 20.0
```

## Usage in Code

```python
prog = shaders.get_or_compile("core/shaders/materials/Water.shader")
```

## Related pages

- [Шейдеры](../shaders.md), [Материалы](../materials.md), [Конвейер](../pipeline.md), [Вода](../water.md), [Raytracing Renderer](../raytracing-renderer.md)

## Verified against

- `core/shaders/materials/` — 9 `.shader`-файлов, заголовки `Shader "..."` (`PBR:7`, `Sky:7`, `Water:66`, `Clouds:7`)
- `core/shaders/internal/` — 22 `.shader`-файла, заголовки `Shader "..."` (каждый `:7`)
- `core/shaders/compute/` — 11 файлов, заголовки `ComputeShader "..."` (`Raytracing.compute:1`, остальные `:7`, кроме `shadow_cascade.compute`)
- `core/shaders/legacy/` — 7 файлов; `core/shaders/include/` — `caustics.glsl`, `area_shadows.glsl`
- `core/shaders/materials/PBR.shader:9-41`, `internal/Default.shader:7-15`, `materials/Sky.shader:9-36`, `materials/Water.shader:66-93`, `materials/Clouds.shader:9-32`
- `core/renderer/shaderp/manager.py`, `core/renderer/shaderp/fallback.py:110`
