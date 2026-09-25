# Cython в скриптах

Тяжёлые вычисления можно вынести в Cython-модуль рядом с обычным скриптом — никаких ручных сборок.

1. `Project → Create → Cython Script` — создаётся `fast_sum.pyx` с шаблоном.
2. В обычном скрипте в той же папке пишется `import fast_sum`.
3. При первом импорте движок сам собирает расширение в `cache/cython` (на Windows встроенный MinGW находится автоматически) и импортирует его.

```python
import move_fast


class Mover:
    speed: float = 5.0

    def on_update(self, dt):
        step = move_fast.clamp_step(self.speed * dt, 0.0, 1.0)
        t = self._entity.transform
        if t:
            t.translate(Vec3(0.0, 0.0, step))
```

```cython
cpdef double clamp_step(double value, double lo, double hi):
    if value < lo:
        return lo
    if value > hi:
        return hi
    return value
```

Правила:

- Имя модуля — верхний уровень: `import foo`, где `foo.pyx` лежит рядом со скриптом. Имена должны быть уникальны в пределах проекта. Соседний `foo.pxd` тоже учитывается при пересборке.
- Пересборка — автоматически, когда `.pyx` новее сборки. Артефакты лежат в `cache/cython`, папка со скриптами не засоряется. Правка `.pyx` во время Play триггерит hot-reload скрипта как обычно.
- Кнопка `Check` проверяет `.pyx` без C-компилятора (только Cython-парсинг и типизация). Полная сборка происходит при первом импорте.
- Если компилятор не найден, в консоли будет понятная ошибка, а скрипт продолжит работать на предыдущей рабочей сборке.
