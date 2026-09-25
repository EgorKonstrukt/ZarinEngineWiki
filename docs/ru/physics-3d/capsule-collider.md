# Capsule-коллайдер

Тела персонажей и пилюли: цилиндр со сферическими крышками, стабилен при вращении.

| Группа / поле инспектора | Default | Смысл |
|---|---|---|
| Collision: Layer / Collision Mask | 0 / 0xFFFF | Фильтрация |
| Shape: Center | начало | Локальный сдвиг |
| Shape: Radius | 0.5 | Масштабируется наибольшей осью |
| Shape: Height | 2.0 | Масштабируется по оси направления |
| Shape: Direction | 1 (Y) | 0 = X, 1 = Y, 2 = Z |
| Is Trigger | выкл | События пересечения без контактного отклика |
| Material: Physic Material | — | Оверрайд ассетом `.zphysmat` |

При дефолтах совпадает с капсулой CharacterController (радиус 0.5, высота 2.0).

Связанное: CharacterController, Sphere-коллайдер, Rigidbody.
