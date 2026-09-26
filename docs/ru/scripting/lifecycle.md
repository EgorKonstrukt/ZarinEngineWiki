# Колбэки жизненного цикла

Все колбэки опциональны. Определяйте только нужное поведению; движок кэширует лукапы методов и пропускает отсутствующие без цены.

| Метод | Короткий алиас | Сигнатура | Когда вызывается |
|---|---|---|---|
| `on_awake` | `awake` | `()` | Один раз при старте Play, до `on_start`; кэши и поиск соседей сюда |
| `on_start` | `start` | `()` | Один раз при старте Play; начальное состояние и первые действия |
| `on_update` | `update` | `(dt)` | Каждый кадр в порядке сцены; `dt` уже умножен на time scale |
| `on_fixed_update` | `fixed_update` | `(dt)` | Каждый шаг физики со стабильным `dt` (`0.02`); силы и движение сюда |
| `on_enable` | `enable` | `()` | При активации сущности (`entity.active = True`) |
| `on_disable` | `disable` | `()` | При деактивации; останавливайте циклы и звуки здесь |
| `on_collision_enter` | — | `(other_id)` или `(other_id, force)` | Начало контакта |
| `on_collision_stay` | — | `(other_id)` или `(other_id, force)` | Продолжение контакта |
| `on_collision_exit` | — | `(other_id)` или `(other_id, force)` | Конец контакта |
| `on_destroy` | `destroy` | `()` | Компонент удалён или сцена выгружена; освобождайте ресурсы здесь |
| `gizmo_lines` | — | `() -> list` | Линии гизмо редактора, работают вне Play |
| `gizmo_meshes` | — | `() -> list` | Меши гизмо редактора, работают вне Play |

Порядок при старте Play: сначала `on_awake` всех скриптованных компонентов, затем `on_start` всех. Не полагайтесь на порядок стартов соседей — ищите соседей лениво или под guards в `on_update`.

Семантика `dt`: рендерный `dt` тянется за slow motion и паузой (time scale 0 морозит обновления); фиксированный `dt` стабилен, и физика со скриптами остаётся детерминированной.

Диспатч столкновений: солвер отдаёт контакты физическому слою, тот маппит тела на id сущностей и дёргает колбэки. Арность меряется один раз по сигнатуре — два параметра получают `(other_id: str, force: float)`, один — только `other_id`. Резолвите другую сущность внутри колбэка только при нужде; сравнение id дешевле.

Enable/disable: тогл `entity.active` стреляет `on_disable`/`on_enable` всех компонентов сущности, включая скрипты. Деактивация в Play ставит поведение на паузу без разрушения состояния инстанса.

Случаи destroy: удаление ScriptComponent в редакторе, удаление сущности, выгрузка сцены, остановка Play. Hot-reload пересоздаёт инстанс, но сохраняет поля инспектора и перезапускает `on_awake`/`on_start`.

Исключения: любая ошибка в любом колбэке перехватывается и пишется как `Script ... error ...` с трейсбеком, именующим скрипт и метод. Кадр, шаг физики и остальные компоненты продолжаются.

```python
class Fighter:
    health: int = 100

    def on_awake(self):
        self.hits = 0
        self.body = self._entity.get_component_by_name("Rigidbody")

    def on_start(self):
        self.alive = True

    def on_update(self, dt):
        if self.health <= 0 and self.alive:
            self.alive = False
            self._entity.active = False

    def on_fixed_update(self, dt):
        if self.body and self.health <= 0:
            self.body.velocity = Vec3(0.0, 0.0, 0.0)

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        self.health = self.health - int(force)

    def on_destroy(self):
        self.hits = 0
        self.body = None
```
