# Обзор форматов файлов

Все форматы — plain text (JSON или блочный формат в духе Unity ShaderLab). Пути внутри файлов хранятся относительно корня проекта, поэтому сцены переезжают между машинами.

| Расширение | Тип | Реальная форма |
|---|---|---|
| `.zpes` | Сцена (JSON) | `{"entities": [{"id", "name", "active", "tags", "layer", "parent", "components", "prefab_guid"?}], ...}` — запись на сущность; словарь каждого компонента несёт `type` и `_key` |
| `.zpep` | Префаб (поддерево JSON) | Та же форма словарей сущностей, что в `.zpes`, со стабильными GUID; инстансы добавляют `prefab_guid` / `prefab_source_id` и поинстансные оверрайды |
| `.zpem` / `.mat` | Материал (JSON) | `{"name", "shader_path", "properties": {...}}` |
| `.shader` | Шейдер (блочный формат) | `Shader "Name" { Properties { } SubShader { Tags { } Pass { GLSLPROGRAM ... } } }`, внутри GLSL вершинный/фрагментный код |
| `.import` | Настройки импорта ассета (JSON) | Сайдкар текстуры/меша/аудио: `{filter_mode, wrap_mode, ...}` рядом с ассетом |
| `.zphysmat` | Физический материал (выбор ресурсом) | На него ссылаются поля `physic_material` коллайдеров через пикер ассетов |
| `.animclip` / `.animcontroller` | Данные анимации (выбор ресурсом) | На них ссылаются компоненты анимации через пикер ассетов |

## Блок сущности `.zpes` (из `Entity.serialize`)

```json
{
  "id": "7f3a...",
  "name": "Ball",
  "active": true,
  "tags": [],
  "layer": 0,
  "parent": null,
  "components": [
    {"type": "Rigidbody", "mass": 1.0, "use_gravity": true, "is_kinematic": false, "_key": "Rigidbody"},
    {"type": "ScriptComponent", "script_path": "assets/ball.py",
     "script_fields": {"push": 8.0, "hits": 0}, "_key": "ScriptComponent"}
  ]
}
```

Поля скриптов сериализуются без потерь типов: `Vec3` как `[x, y, z]`, `Enum` как значение, `Curve` как `{"keys": [{"time", "value", "in_tangent", "out_tangent", "tangent_mode"}], "pre_wrap", "post_wrap"}`. Ссылки на сущности хранятся UUID-строками и резолвятся в живые объекты перед каждым вызовом скрипта. Полный словарь `Rigidbody` дополнительно несёт `drag`, `angular_drag`, массивы `freeze_pos` / `freeze_rot` поверх сокращённых ключей в примере выше.

## Материал (реальный файл `assets/materials/ProBuilderPrototype.mat`)

```json
{"name": "ProBuilderPrototype", "properties": {"_BaseColor": [1.0, 1.0, 1.0, 1.0], "_EmissionColor": [0.0, 0.0, 0.0, 0.0], "_EmissionIntensity": 0.0, "_Metallic": 0.0, "_Smoothness": 0.5, "albedo_texture": "../textures/prototype_texture.png"}, "shader_path": "default"}
```

`Material.save` пишет ровно `{"name", "shader_path", "properties"}` с релятивизованными путями текстур; `Material.load` одинаково принимает `.mat` и `.zpem`, при пустом пути шейдера откатывается на `"default"`.

## Блок шейдера (реальный файл `core/shaders/internal/Default.shader`, начало)

```shader
Shader "Zarin/Default"
{
    Properties
    {
    }

    SubShader
    {
        Tags { "RenderType" = "Opaque" }

        Pass
        {
            GLSLPROGRAM
            #version 430 core
            layout(location = 0) in vec3 in_position;
```

Материалы объявляют редактируемые в рантайме свойства (цвета, числа, текстуры, векторы, диапазоны), которые открывает блок `Properties { }`; мешевые данные уходят на GPU как `float32`, сконвертированный при загрузке из движковой математики `float64`.

## Настройки импорта (реальный файл `assets/textures/prototype_texture.png.import`)

```json
{"anisotropic": 4, "compression": "none", "filter_mode": "trilinear", "max_size": 2048, "srgb": true, "wrap_mode": "repeat"}
```

Мешевые сайдкары `.import` несут ту же идею для геометрии: масштаб, флаги генерации нормалей/тангентов, угол сглаживания. Импортёр ищет `<ассет>.import` рядом с ассетом (включая фолбэки относительно корня проекта), затем откатывается на дефолты.

См. также: Примеры скриптов (хранение полей), ScriptComponent (как персистятся `script_fields`), Указатель API (точки входа загрузчиков).
