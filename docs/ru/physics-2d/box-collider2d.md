# Box Collider 2D

Прямоугольник в плоскости: платформы, стены, хитбоксы.

| Группа / поле инспектора | Смысл |
|---|---|
| Collision: Layer / Collision Mask | Фильтрация |
| Shape: Offset | Сдвиг в плоскости `[ox, oy]` |
| Shape: Size | Габариты в плоскости |
| Is Trigger | События пересечения без контактного отклика |
| Material: Physic Material | Оверрайд ассетом `.zphysmat` |

Маппится на солвер как плоский бокс `[sx, sy, 1.0]`. Комбинируйте с Rigidbody2D; не смешивайте 2D- и 3D-компоненты на одной сущности.

Связанное: Rigidbody 2D, Circle Collider 2D, Box-коллайдер (3D).
