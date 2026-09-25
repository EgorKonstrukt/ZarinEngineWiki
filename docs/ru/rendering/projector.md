# Проектор Projector

Проецирует текстуру из фрустума: слайд-проектор, фальшивый оконный свет, луч граффити.

| Группа / поле инспектора | Default | Смысл |
|---|---|---|
| Texture: texture_path | — | Проецируемое изображение (png/jpg/jpeg) |
| Texture: color | белый | Множитель оттенка |
| Texture: intensity | 1.0 | Яркость, 0–1000 |
| Projection: range | 10.0 | Дистанция броска |
| Projection: spot_angle | 30° | Угол фрустума, 1–179 |
| Projection: aspect_ratio | 1.0 | Ширина/высота фрустума |
| Projection: near_plane / far_plane | 0.1 / 100 | Срез глубины |
| Options: flip_x / flip_y | выкл / вкл | Зеркаляние картинки |
| Options: cast_shadows | вкл | Проекция с тенями |

Гизмо во вьюпорте рисует конус проекции с корректным основанием. Наводится трансформом как Spot Light.

Связанное: Spot Light, тени (проход projector shadows).
