# Колбэки жизненного цикла

Все колбэки опциональны. Определяйте только то, что нужно поведению.

| Метод | Короткий алиас | Сигнатура | Когда вызывается |
|---|---|---|---|
| `on_awake` | `awake` | `()` | Один раз при старте Play, до `on_start` |
| `on_start` | `start` | `()` | Один раз при старте Play |
| `on_update` | `update` | `(dt)` | Каждый кадр, `dt` уже умножен на time scale |
| `on_fixed_update` | `fixed_update` | `(dt)` | Каждый шаг физики (`dt = 0.02` по умолчанию) |
| `on_enable` | `enable` | `()` | При активации сущности |
| `on_disable` | `disable` | `()` | При деактивации сущности |
| `on_collision_enter` | — | `(other_id)` или `(other_id, force)` | Начало контакта |
| `on_collision_stay` | — | `(other_id)` или `(other_id, force)` | Продолжение контакта |
| `on_collision_exit` | — | `(other_id)` или `(other_id, force)` | Конец контакта |
| `on_destroy` | `destroy` | `()` | При удалении компонента или выгрузке сцены |
| `gizmo_lines` | — | `() -> list` | Линии гизмо в редакторе, работают и вне Play |
| `gizmo_meshes` | — | `() -> list` | Меши гизмо в редакторе, работают и вне Play |

Правила:

- Алиасы резолвятся для каждого метода с приоритетом `on_*`: если класс определяет и `update`, и `on_update`, работает только `on_update`.
- Колбэки столкновений принимают один или два аргумента. Движок один раз смотрит сигнатуру: с двумя параметрами вторым приходит сила удара (`float`), иначе передаётся только id другой сущности (`str`).
- `self._entity` выставляется движком перед каждым вызовом, включая `on_awake` и `on_start`.
- Любое исключение в колбэке перехватывается и пишется в лог как `Script ... error ...` с трейсбеком. Кадр продолжается со следующего компонента.

```python
class Fighter:
    health: int = 100

    def on_awake(self):
        self.hits = 0

    def on_update(self, dt):
        if self.health <= 0:
            self._entity.active = False

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        self.health = self.health - int(force)

    def on_destroy(self):
        self.hits = 0
```
