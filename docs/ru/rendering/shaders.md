# Шейдеры (.shader)

Кастомный текстовый формат в стиле Unity ShaderLab: шапка `Properties`, понятная редактору, плюс сырой GLSL для GPU. Минимальный пример:

```shader
Shader "Zarin/Unlit" {
  Properties {
    _BaseColor("Base Color", Color) = (1, 1, 1, 1)
    _BaseMap("Base Map", 2D) = "white" {}
  }
  SubShader {
    Tags { "RenderType" = "Opaque" }
    Pass {
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
      #version 430 core
      uniform vec4 u_albedo_color;
      in vec2 v_uv;
      out vec4 fragColor;
      void main() {
        fragColor = u_albedo_color;
      }
      ENDGLSL
    }
  }
}
```

Правила формата, сверено с загрузчиком:

- Записи `Properties` как `[_Attr] _Name("Display", Type) = default`. Типы: `Color`/`Vector` как `(1,1,1,1)`, `Float`/`Range(a,b)` как float, `Int` как int, `2D`/`Cube` как `"white"` / `"bump"` плюс `{}`.
- В файле несколько блоков `SubShader`; загрузчик пробует по порядку, затем опциональный `Fallback "Name"`, если упали все.
- Каждый блок `GLSLPROGRAM … ENDGLSL` делится на вершины/фрагменты по маркеру `// @FRAGMENT`.
- Загрузчик инжектит атрибуты GPU-инстансинга (`in_model0-3`, SSBO-биндинги 4/5 на 430+), UV-оверрайды (`u_uv_scale`, `u_uv_offset`, `u_uv_world_scale`) и резолвит маркеры инклюдов area-теней и каустики.
- Поиск шейдеров — дерево `core/shaders` корня движка: `materials`, `internal`, `compute`, `include`, `legacy`.

Процесс: дублируйте `Unlit.shader`, переименуйте путь `Shader "..."`, объявите входы в Properties (автопоявятся в каждом материале на нём), пишите проходы, назначьте на материал и итерируйте с горячим превью. Ошибки компиляции печатаются с файлом и строкой — чините сверху вниз, первая обычно каскадит.

Фолбэк компиляции: сначала пробуется `#version 430 core`; при провале повтор как `#version 330 core` с принудительно выкл инстансингом и скиннингом. Эффекты, фундаментально требующие 430 (SSBO-пути), visibly деградируют — смотрите notice в консоли.

Встроенная библиотека в `core/shaders`: 9 шейдеров материалов (`PBR`, `Unlit`, `Sky`, `Skybox`, `Water`, `WaterSim`, `Tree`, `Clouds`, `CloudLayer`), 20+ внутренних (`Default`, `Shadow`, `Particle`, `ParticleGpu`, `Sprite`, `Text`, `Video`, `Icon`, `Gizmo`, `Grid`, `Outline`, `ObjectFx`, `Projector`, `GaussianSplat`, `Caustics`, `Underwater` и другие) и общие сниппеты `include/` (`area_shadows.glsl`, `caustics.glsl`).

Для нодового авторства — Shader Graph в руководстве по редактору; для значений на материал — материалы.

Связанное: материалы, MeshRenderer (UV-оверрайд uniforms), свет (uniform света), Shader Graph.
