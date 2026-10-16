# Вьюпорт, камера, гизмо

> `SceneViewport` (`editor/viewport/viewport.py`) — `QOpenGLWidget` вида сцены: управление камерой, пикинг/выделение, трансформ-гизмо, миксины рендера и ввода.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `SceneViewport` | `(engine, parent=None)` | Миксины `ViewportCameraMixin`, `ViewportOverlayMixin`, `ViewportSelectionMixin`, `ViewportRenderMixin`, `ViewportInputMixin` |
| `camera` / `gizmo` / `renderer` | свойства | Доступ к подобъектам |
| `selected_entities` | `() -> list` | Текущее выделение |
| `initializeGL` / `resizeGL` / `paintGL` | `(w, h)` / `()` | Жизненный цикл GL |
| `load_config` | `(config) -> None` | Восстановление сохранённых настроек |
| `showEvent` / `hideEvent` | `(event)` | Старт/стоп отрисовки |
| `on_dock_top_level_changed` | `(floating: bool)` | Обработка отстыковки дока |
| Камера: `_on_fov_changed` / `_on_near_changed` / `_on_far_changed` | `(value: float)` | В `viewport/viewport_camera.py` |
| Камера: `_on_move_speed_changed` / `_on_rotate_speed_changed` | `(value: float)` | Скорости облёта/орбиты |
| Камера: `_on_render_scale_changed` | `(value: int)` | Масштаб разрешения рендера |
| Камера: `set_grid_step` / `snap_to_grid` / `snap_vec3_to_grid` | `(step: float)` / `(value: float)` / `(v: Vec3)` | Привязка к сетке |
| Выделение: `set_selected_entity` / `set_selected_entities` | `(entity)` / `(entities: list)` | В `viewport/viewport_selection.py` |
| Выделение: `_delete_selected` / `_copy_selected_entities` / `_paste_entities` / `_frame_selected` | `()` | Операции над выделением |
| Выделение: `_emit_create_request` | `(obj_type="empty", subtype=None)` | Точка входа create-меню вьюпорта |
| Клавиши ввода | `KEY_W/A/S/D/Q/E/R/F`, `KEY_SHIFT/CTRL/ALT/SPACE/DELETE`, `MOUSE_L/R/M` из `core/input/constants.py` | В `viewport/viewport_input.py`; C/V/D — копирование/вставка/дублирование |
| `OverlayWidget` | `(viewport, parent=None)` + `set_visible(bool)` | `viewport/overlay_widget.py` |
| `ProgressToast` | `(parent=None)` + `reposition()` | Тосты задач в `viewport/progress_toast.py` |

## Usage in Editor

Режим облёта: `WASD` + осмотр правой кнопкой, пан средней, долли колесом. Режим орбиты: правая кнопка вращает вокруг 3D-курсора, `Shift+RMB` ставит курсор, `Ctrl+MMB`/колесо — долли (текст тултипа режима навигации). `C`/`V`/`D` работают с выделением (пути копирования/вставки/дублирования в обработчике клавиш). Гизмо следует за `_update_gizmo_pivot`; мульти-перетаскивание фиксируется через `_commit_multi_transform`. FOV/near/far/скорости/масштаб рендера правятся через контролы камеры.

## Usage in Code

```python
vp.set_selected_entity(ent)
vp.set_selected_entities([a, b])
vp.snap_vec3_to_grid(pos)   # привязка Vec3 к шагу сетки
vp.set_grid_step(0.5)
```

## Скриншоты

![Вьюпорт редактора ZarinEngine (по центру) в главном окне](../../assets/screenshots/editor-main.png)

> Вид сцены с камерой, сеткой и тестовой моделью дракона; справа — Game view.
> Файл: `assets/screenshots/editor-main.png` (проверенная копия
> `zarin_screenshot.png` из репозитория движка).

## Related

- Главное окно (тулбар с Play и режимом навигации), Иерархия (зеркало выделения), Инспектор (правка выделения)
- Гизмо: `editor/gizmo/pb_scale_gizmo.py`, `editor/gizmo/phys_bone_grab.py`
- Вьюпорт дока Play: `editor/panels/play/viewport.py`

## Verified against

- `editor/viewport/viewport.py`, `editor/viewport/viewport_camera.py`, `editor/viewport/viewport_selection.py`, `editor/viewport/viewport_input.py`
- `editor/viewport/overlay_widget.py`, `editor/viewport/progress_toast.py`, `editor/viewport/perf.py`, `editor/viewport/projection.py`, `editor/viewport/undo_filter.py`
- `editor/main_window/toolbar.py` (тултип навигации), `editor/scene_viewport.py`, `editor/scene_toolbar.py`
