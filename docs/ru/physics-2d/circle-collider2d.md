# Circle Collider 2D

Диск в плоскости: мячи, колёса, радиальные триггеры.

| Группа / поле инспектора | Смысл |
|---|---|
| Collision: Layer / Collision Mask | Фильтрация |
| Shape: Offset | Сдвиг в плоскости `[ox, oy]` |
| Shape: Radius | Радиус диска |
| Is Trigger | События пересечения без контактного отклика |
| Material: Physic Material | Оверрайд ассетом `.zphysmat` |

Маппится на солвер как примитив сферы. Стабильно катится по земле из Box Collider 2D на низком трении.

Связанное: Rigidbody 2D, Box Collider 2D, Sphere-коллайдер (3D).
