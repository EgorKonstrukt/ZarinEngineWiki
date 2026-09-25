# GS Volume Collider

Объёмы коллизий из Gaussian-splat захватов.

| Группа / поле инспектора | Смысл |
|---|---|
| Gs Volume Collider: Splat Path | Исходный захват |
| Voxel Size | Шаг дискретизации объёма |
| Opacity Cutoff | Порог плотности |
| Dilation | Расширение объёма в вокселях |
| Max Boxes | Бюджет бокс-аппроксимации |
| Center | Локальный сдвиг |
| Layer / Collision Mask / Is Trigger | Стандартная фильтрация |
| Physic Material | Оверрайд ассетом `.zphysmat` |

Позволяет ходить и сталкиваться в splat-сценах в стиле фотограмметрии, где нет меш-геометрии.

Связанное: Gaussian Splat Renderer, Box-коллайдер, Mesh-коллайдер.
