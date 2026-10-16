# Material Wizard and Previews

> Live GL previews: `MaterialPreviewWidget` (`editor/material_preview.py`) renders material properties on a sphere; `ModelPreviewWidget` (`editor/model_preview.py`) renders raw mesh buffers; `editor/matwiz/` hosts the material creation wizard.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `MaterialPreviewWidget` | `(parent=None)` | `QOpenGLWidget`; orbit with mouse, zoom with wheel |
| `MaterialPreviewWidget.set_properties` | `(*args, **kwargs)` | Pushes material props (color/texture/roughness-style keys resolved via `_get_pv`) |
| `MaterialPreviewWidget._resolve_tex` | `() -> str` | Resolves the preview texture path |
| `MaterialPreviewWidget` GL | `initializeGL/resizeGL/paintGL`, `_release_resources()`, `hideEvent` | Resource lifecycle |
| `ModelPreviewWidget` | `(parent=None)` | `QOpenGLWidget`; orbit + wheel zoom |
| `ModelPreviewWidget.set_mesh` | `(verts: np.ndarray, indices: np.ndarray, normals: Optional[np.ndarray] = None)` | Uploads buffers; normals auto-computed by `_compute_normals` when omitted |
| `ModelPreviewWidget._build_mesh` | `() -> bool` | (Re)builds GL buffers, called from `paintGL` when dirty |

## Usage in Editor

Material assets and material fields show a `MaterialPreviewWidget` thumbnail that updates as properties change; dragging rotates the preview sphere, wheel zooms. Model import dialogs and mesh fields embed `ModelPreviewWidget` fed with the imported vertex/index buffers. The Material Wizard (`editor/matwiz/`) walks through creating a material asset step by step.

## Usage in Code

```python
preview = MaterialPreviewWidget(parent=page)
preview.set_properties(color=(1, 0.5, 0.2), roughness=0.4)

model_preview = ModelPreviewWidget(parent=page)
model_preview.set_mesh(verts, indices)  # normals computed automatically
```

## Related

- Project Panel (material/model assets), Inspector (material fields), Model import (`handlersp/drop_assets.py`)
- Rendering: Materials, Shaders, Mesh Filter / Mesh Renderer

## Verified against

- `editor/material_preview.py`, `editor/model_preview.py`, `editor/matwiz/`
