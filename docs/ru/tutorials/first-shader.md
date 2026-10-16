# Урок: первый шейдер

Кастомный шейдер материала за 5 шагов: файл `.shader` (`Properties` + GLSL), материал `.zpem`, назначение на `MeshRenderer`. Синтаксис — из `core/assets/matp/shader.py` (`_SHADER_PROP_RE`, `_parse_shader_default`); загрузка материалов — из `core/assets/matp/materials.py` (принимает `.mat` / `.zpem`, `load_shader_properties` требует расширение `.shader`); слоты — из `core/components/rendering/renderers/mesh_renderer.py` и `mesh_filter.py`.

Формат строки свойств: `[Attr] _Name("Display Name", Type) = default`. Типы: `Color` / `Vector` → `(1, 1, 1, 1)`; `Float` / `Range(a, b)` → число; `Int` → целое; `2D` / `Cube` → `"white" {}`. Структура: `Shader "Zarin/MyShader" { Properties { … } SubShader { Tags { … } Pass { GLSLPROGRAM … // @FRAGMENT … ENDGLSL } } }` (см. `core/shaders/materials/Unlit.shader`).

## Шаги

1. **Создайте шейдер.** Панель Project → `Create → Shader`, сохраните как `assets/shaders/pulse.shader`, вставьте код ниже. Маркеры `GLSLPROGRAM` / `// @FRAGMENT` / `ENDGLSL` обязательны — экстрактор делит вершинный/фрагментный код по ним.
2. **Создайте материал.** `Create → Material`, сохраните как `assets/materials/pulse.zpem`, вставьте JSON ниже (`shader_path` + `properties`; при пропуске берутся дефолты из блока `Properties`).
3. **Создайте держатель меша.** Сущность `Pulser` → `Add Component → MeshFilter` (`Source = <любой .obj/.fbx>`, например куб; `Mesh = mesh`) → `Add Component → MeshRenderer`.
4. **Назначьте.** В `MeshRenderer → Materials → [0] → Material` выберите `assets/materials/pulse.zpem`. Inspector покажет `_PulseSpeed` / `_PulseColor` из блока `Properties` шейдера.
5. **Нажмите Play.** Фрагмент пульсирует между albedo и `_PulseColor` с частотой `_PulseSpeed` Гц. Предупреждение `Shader path '…' not found, remapped` означает неверный `shader_path` — он резолвится от корня проекта, затем от корня движка / `core/shaders/`.

## Полный код — `assets/shaders/pulse.shader`

```glsl
Shader "Zarin/Pulse"
{
    Properties
    {
        [MainColor] _BaseColor("Base Color", Color) = (1, 1, 1, 1)
        _PulseColor("Pulse Color", Color) = (1, 0.2, 0.4, 1)
        _PulseSpeed("Pulse Speed", Range(0, 8)) = 2.0
    }

    SubShader
    {
        Tags { "RenderType" = "Opaque" }

        Pass
        {
            GLSLPROGRAM
            #version 430 core
            layout(location = 0) in vec3 in_position;
            layout(location = 1) in vec3 in_normal;
            layout(location = 2) in vec2 in_uv;
            uniform mat4 u_model;
            uniform mat4 u_view;
            uniform mat4 u_proj;
            out vec2 v_uv;
            void main() {
                v_uv = in_uv;
                gl_Position = u_proj * u_view * u_model * vec4(in_position, 1.0);
            }

            // @FRAGMENT

            #version 330 core
            in vec2 v_uv;
            out vec4 frag_color;
            uniform vec4 _BaseColor;
            uniform vec4 _PulseColor;
            uniform float _PulseSpeed;
            uniform float u_time;
            void main() {
                float k = 0.5 + 0.5 * sin(6.28318 * _PulseSpeed * u_time);
                frag_color = mix(_BaseColor, _PulseColor, k);
            }
            ENDGLSL
        }
    }
}
```

## Полный код — `assets/materials/pulse.zpem`

```json
{
  "name": "pulse",
  "shader_path": "assets/shaders/pulse.shader",
  "properties": {
    "_BaseColor": [1.0, 1.0, 1.0, 1.0],
    "_PulseColor": [1.0, 0.2, 0.4, 1.0],
    "_PulseSpeed": 2.0
  }
}
```

`MeshRenderer.materials` — это `[{"path": "assets/materials/pulse.zpem"}]` (первая запись = сабмеш 0; `get_material_path(i)` откатывается на последнюю запись). Если материал остался плоским белым — шейдер не скомпилировался, смотрите запись в логе консоли (ресурс `console://log` или док Console).

## Проверено по коду

`core/assets/matp/shader.py`, `core/assets/matp/materials.py`, `core/shaders/materials/Unlit.shader`, `core/components/rendering/renderers/mesh_renderer.py`, `core/components/rendering/renderers/mesh_filter.py`.
