# Rigidbody 2D

Плоская динамика для скролл-шутеров и top-down игр.

| Поле | Смысл |
|---|---|
| Mass | Вес тела |
| Drag / Angular Drag | Плоское демпфирование |
| Gravity Scale | Локальный множитель гравитации |
| Is Kinematic | Ведётся трансформом, толкает других |
| Freeze Rotation | Лочит плоское вращение |

Тот же API из скриптов, что в 3D (`velocity`, `add_force`, `add_torque`, `add_impulse`). Солвер флагует тело как 2D и обнуляет внеплоскостные скорости каждый шаг.

Связанное: Box Collider 2D, Circle Collider 2D, Rigidbody (3D).
