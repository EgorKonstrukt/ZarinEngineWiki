# Камера Camera

Рисует сцену из точки сущности. Одна на вид: главная игровая камера, камера UI-плоскости, камеры катсцен.

| Группа / поле инспектора | Смысл |
|---|---|
| Projection: FOV | Перспективный угол обзора, 1–179°, по умолчанию 60 |
| Projection: Near / Far | Плоскости отсечения, по умолчанию 0.01 / 1000 |
| Projection: Ortho Size | Ортографическая вертикальная полувысота, по умолчанию 5 |
| Rendering: Depth | Приоритет порядка отрисовки, −100…100 |
| Render Resolution: Resolution Mode | Native или Custom |
| Render Resolution: Viewport Width / Height | Кастомный размер, например 1920×1080 |
| render_scale | Множитель разрешения 0.1–1.0 |

Математика: вид — `look_at(позиция, позиция + вперёд, вверх)`; проекция — `perspective(fov, aspect, near, far)` или `orthographic(-half_w, half_w, -ortho_size, ortho_size, near, far)`, где `half_w = ortho_size * aspect`.

Связанное: камера редактора (только монтаж сцены), свет и тени, режимы разрешения в build-профилях.
