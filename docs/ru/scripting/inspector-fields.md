# Поля инспектора и Range

Аннотированные атрибуты класса автоматически появляются в инспекторе, а их значения хранятся в сцене (`.zpes`). Приватные имена на `_` пропускаются, кроме специального `_inspector_buttons`.

```python
from enum import Enum
from typing import Annotated
from core.maths.math3d import Vec3
from core.foundation.curve import Curve


class Mode(Enum):
    IDLE = 0
    PATROL = 1


class Enemy:
    speed: Annotated[float, Range(0.0, 10.0)] = 5.0
    health: int = 100
    title: str = "grunt"
    active: bool = True
    offset: Vec3 = Vec3(0.0, 1.0, 0.0)
    mode: Mode = Mode.PATROL
    falloff: Curve = Curve()
    target: 'Entity' = None
    _inspector_buttons = [("explode", "Explode")]

    def explode(self):
        self.health = 0
```

| Аннотация | Виджет инспектора |
|---|---|
| `float` | Числовое поле |
| `int` | Целое поле |
| `bool` | Чекбокс |
| `str` | Строка |
| `Annotated[float, Range(min, max[, step])]` | Слайдер, шаг по умолчанию `0.01` |
| `Annotated[int, Range(min, max)]` | Целый слайдер, шаг по умолчанию `1` |
| `Vec2` / `Vec3` / `Vec4` | Векторные поля |
| Подкласс `Enum` | Выпадающий список |
| `'Entity'` | Пикер сущности (хранится UUID, в скрипт приходит живой объект) |
| `Curve` | Редактор кривой |

Заметки:

- `Range` — это `Range(min_value=0.0, max_value=1.0, step=None)`. Обычная последовательность `[min, max]` или `(min, max[, step])` в метаданных `Annotated` работает так же.
- Пикеры ресурсов (меш, материал, текстура, аудио, префаб, сцена, физический материал) используют те же фильтры диалогов, что и встроенные компоненты.
- `_inspector_buttons = [(имя_метода, подпись), ...]` рисует кнопки, вызывающие метод на живом инстансе.
- Вверху компонента — неизменяемое поле `Script` с именем файла: клик показывает файл в панели Project, кнопка `...` открывает встроенный редактор скриптов.
