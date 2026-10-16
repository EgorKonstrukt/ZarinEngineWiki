# Мастер материалов и превью

> Живые GL-превью: `MaterialPreviewWidget` (`editor/material_preview.py`) рисует свойства материала на сфере; `ModelPreviewWidget` (`editor/model_preview.py`) рисует сырые меш-буферы; `editor/matwiz/` — визард создания материала.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `MaterialPreviewWidget` | `(parent=None)` | `QOpenGLWidget`; вращение мышью, зум колесом |
| `MaterialPreviewWidget.set_properties` | `(*args, **kwargs)` | Проталкивает свойства материала (ключи цвета/текстуры/roughness-стиля резолвятся через `_get_pv`) |
| `MaterialPreviewWidget._resolve_tex` | `() -> str` | Резолвит путь текстуры превью |
| GL `MaterialPreviewWidget` | `initializeGL/resizeGL/paintGL`, `_release_resources()`, `hideEvent` | Жизненный цикл ресурсов |
| `ModelPreviewWidget` | `(parent=None)` | `QOpenGLWidget`; вращение + зум колесом |
| `ModelPreviewWidget.set_mesh` | `(verts: np.ndarray, indices: np.ndarray, normals: Optional[np.ndarray] = None)` | Загружает буферы; нормали без ввода считает `_compute_normals` |
| `ModelPreviewWidget._build_mesh` | `() -> bool` | (Пере)создаёт GL-буферы, вызывается из `paintGL` по флагу |

## Usage in Editor

Ассеты материалов и поля материалов показывают миниатюру `MaterialPreviewWidget`, обновляющуюся при смене свойств; перетаскивание вращает сферу превью, колесо зумит. Диалоги импорта моделей и меш-поля встраивают `ModelPreviewWidget`, питаемый импортированными вершинными/индексными буферами. Визард материалов (`editor/matwiz/`) пошагово ведёт создание ассета материала.

## Usage in Code

```python
preview = MaterialPreviewWidget(parent=page)
preview.set_properties(color=(1, 0.5, 0.2), roughness=0.4)

model_preview = ModelPreviewWidget(parent=page)
model_preview.set_mesh(verts, indices)  # нормали посчитаются автоматически
```

## Related

- Панель проекта (ассеты материалов/моделей), Инспектор (поля материалов), Импорт моделей (`handlersp/drop_assets.py`)
- Рендеринг: Materials, Shaders, Mesh Filter / Mesh Renderer

## Verified against

- `editor/material_preview.py`, `editor/model_preview.py`, `editor/matwiz/`
