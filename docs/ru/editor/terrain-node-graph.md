# Граф террейна и NodeGraphQt

> Процедурные высоты (`editor/terrain_graph/` + `editor/panels/terrain/`): узлы шума/масок/фильтров считаются на GPU, запекаются в heightmap через `generate_shader`, превью на каждый узел.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `generate_shader` | `(graph, resolution: int) -> Tuple[str, dict, float]` | `terrain_graph/code_generator.py`; возвращает исходник GLSL, юниформы, время запекания |
| `run_shader` / `run_preview_shader` | `(source: str, resolution: int, uniforms: Optional[Dict[str, float]] = None) -> Optional[np.ndarray]` | `terrain_graph/gpu_runner.py`; `_ensure_ctx()` сторожит контекст |
| `release` / `clear_cache` | `()` | Разборка `gpu_runner.py` |
| `save_graph` / `load_graph` | `(graph, path: str) -> bool` | `terrain_graph/graph_serializer.py` |
| `compute_node_preview` | `(target_node, graph, resolution: int = 64) -> Optional[np.ndarray]` | `terrain_graph/node_preview.py` — запекание только подграфа предков |
| `update_all_previews` / `clear_preview_cache` | `(graph, resolution=64)` / `()` | Пакетное обновление / сброс |
| `NodePreviewWidget` | `preview_widget.py`, наследник `NodeBaseWidget` | Миниатюра heightmap внутри узла |
| `TerrainGraphCollabBridge` | `QObject` в `terrain_graph/collab_bridge.py` | Пересылает правки графа в коллаб-сессию |
| Генераторы | `PerlinNoiseNode`, `RidgedNoiseNode`, `BillowNoiseNode`, `VoronoiNode`, `ConstantNode`, `ContinentMaskNode`, `DuneNode` (`nodesp/generators.py`) | Источники высот |
| Фильтры | `DomainWarpNode`, `ErosionNode`, `TerraceNode`, `PlateauNode`, `SlopeMaskNode`, `StrataNode`, `SharpenNode`, `SmoothNode`, `NormalizeNode`, `InvertNode`, `HeightBiasNode`, `RiverNode`, `ClampNode`, `RemapNode` (`nodesp/filters.py`) | Формующие проходы |
| Комбинатор + сток | `AddNode`, `SubtractNode`, `MultiplyNode`, `BlendNode`, `MaxNode`, `MinNode`, `HeightOutputNode` (`nodesp/output.py`) | Миксеры и сток графа |
| Панель | `editor/panels/terrain/panel.py` + `graph_widget.py` | Док с канвой |

## Usage in Editor

Док террейна: узлы-генераторы (Perlin/Ridged/Voronoi...), формовка фильтрами (Erosion/Terrace/DomainWarp...), сведение миксеров в `HeightOutputNode`; каждый узел показывает живое превью `NodePreviewWidget` (по умолчанию 64 px), посчитанное только по его предкам. Запекание выполняет `generate_shader` в целевом разрешении и пишет heightmap в компонент террейна. Файлы графов сохраняются/грузятся через сериализатор; правки уходят коллабораторам через мост.

## Usage in Code

```python
from editor.terrain_graph.code_generator import generate_shader
from editor.terrain_graph.gpu_runner import run_shader
from editor.terrain_graph.graph_serializer import save_graph, load_graph
from editor.terrain_graph.node_preview import compute_node_preview

source, uniforms, bake_time = generate_shader(graph, resolution=512)
height = run_shader(source, resolution=512, uniforms=uniforms)
save_graph(graph, "assets/terrain/island.zterrain")
thumb = compute_node_preview(node, graph, resolution=64)
```

## Related

- Граф шейдеров (соседняя нодовая система), компонент Terrain, Коллаборация (мост графа)
- GPU-журнал: `tools/gpuj/`

## Verified against

- `editor/terrain_graph/code_generator.py`, `editor/terrain_graph/gpu_runner.py`, `editor/terrain_graph/graph_serializer.py`, `editor/terrain_graph/node_preview.py`, `editor/terrain_graph/preview_widget.py`, `editor/terrain_graph/collab_bridge.py`, `editor/terrain_graph/glsl_chunks.py`
- `editor/terrain_graph/nodesp/base.py`, `editor/terrain_graph/nodesp/generators.py`, `editor/terrain_graph/nodesp/filters.py`, `editor/terrain_graph/nodesp/output.py`
- `editor/panels/terrain/panel.py`, `editor/panels/terrain/graph_widget.py`
