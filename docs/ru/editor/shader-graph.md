# Граф шейдеров

> Нодовое авторство шейдеров (`editor/shader_graph/`): типизированные узлы в `nodesp/` на канве `NodeGraphQt`, компиляция в GLSL через `code_generator.generate_shader_code`.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `generate_shader_code` | `(graph, shader_name: str = "Custom/GeneratedShader") -> str` | Точка входа `shader_graph/code_generator.py` |
| `_generate_vertex_section` / `_generate_fragment_section` | `(graph, nodes, uid_map, vertex_output/fragment_output) -> str` | Эмиттеры стадий |
| `_empty_shader` | `(name) -> str` | Заглушка, если в графе нет выходного пути |
| База узлов | `_ShaderNode(BaseNode)`; `_InputNode`, `_OutputNode`, `_MathNode`, `_TextureNode`, `_VectorNode` в `nodesp/base.py` | Каждый узел реализует `generate_code(uid_map)` |
| Узлы входов | VertexPosition, UV, Normal, Time, Color, Float, Texture2D (`nodesp/inputs_outputs.py`) | Константы и входы меша/шейдера |
| Узлы выходов | VertexOutput, FragmentOutput (`nodesp/inputs_outputs.py`) | Стоки графа по стадиям |
| Узлы математики/векторов | Add, Multiply, Subtract, Lerp, Normalize, DotProduct, Fresnel, Step, Clamp (`nodesp/math_vector.py`) | Эмиттеры GLSL-выражений |

## Usage in Editor

Док графа шейдеров показывает палитру узлов (входы, математика, текстуры, выходы); связи соединяют типизированные порты; канва требует ровно один вершинный и один фрагментный путь к выходным узлам; Apply перегенерирует GLSL через `generate_shader_code` и назначает его правимому ассету шейдера. Пустые/несвязные графы откатываются на `_empty_shader`, так что материал не ломается.

## Usage in Code

```python
from editor.shader_graph.code_generator import generate_shader_code

glsl = generate_shader_code(graph, shader_name="Custom/MyEffect")
```

## Related

- Граф террейна (родственная нодовая система, домен высот), Мастер материалов и превью
- Рендеринг: Shaders, Materials

## Verified against

- `editor/shader_graph/code_generator.py`, `editor/shader_graph/__init__.py`
- `editor/shader_graph/nodesp/base.py`, `editor/shader_graph/nodesp/inputs_outputs.py`, `editor/shader_graph/nodesp/math_vector.py`
- `editor/NodeGraphQt/`
