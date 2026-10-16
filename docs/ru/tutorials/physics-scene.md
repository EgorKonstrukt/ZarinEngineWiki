# Физическая сцена

Мяч падает на землю, толкается клавишами и считает удары. Четыре шага, все имена компонентов и полей — настоящие.

## 1. Сущность земли

- Кнопка добавления в `Hierarchy` (`Create Entity`) → переименовываем в `Ground`.
- `Inspector → Add Component` → добавляем `BoxCollider`. В инспекторе задаём `Size = Vec3(10.0, 1.0, 10.0)`; `Center` оставляем нулевым, `Is Trigger` выключен — нетриггерный коллайдер блокирует и сообщает о контактах.
- `Rigidbody` земле не нужен: без него она работает как статичное препятствие.

## 2. Мяч с Rigidbody

- Создаём сущность `Ball`, добавляем `Rigidbody`, затем `SphereCollider` (каждый через `Add Component`).
- Инспектор `Rigidbody`: `Mass = 1.0`, `Drag = 0.0`, `Use Gravity` включён, `Is Kinematic` выключен. Это настоящие поля (`mass`, `drag`, `angular_drag`, `use_gravity`, `is_kinematic`); для фиксации движения есть поосевые флаги заморозки.
- Поднимаем `Ball` (`Transform`, позиция `y = 5.0`), чтобы падение было видно.

## 3. Скрипты: толчок в fixed-шаге, счётчик ударов

- `Project → Create → Python Script` → `ball.py`; вешаем через `Add Component → Scripts` на `Ball`.

```python
class Ball:
    push: float = 8.0
    hits: int = 0

    def on_awake(self):
        self.body = self._entity.get_component_by_name("Rigidbody")

    def on_fixed_update(self, dt):
        if not self.body:
            return
        if Input.GetKey(KeyCode.W):
            self.body.add_force(Vec3(0.0, 0.0, self.push))
        if Input.GetKey(KeyCode.S):
            self.body.add_force(Vec3(0.0, 0.0, -self.push))
        if Input.GetKeyDown(KeyCode.SPACE):
            self.body.add_impulse(Vec3(0.0, 5.0, 0.0))

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        Logger.info(f"hit {other_id} force {force} total {self.hits}")
```

Правила движка: силы и импульсы — в `on_fixed_update(dt)` (стабильный шаг, по умолчанию `0.02`); `add_force` копится до потребления солвером, `add_impulse` — мгновенный пинок с учётом массы (`impulse * (1/mass)`); арность колбэка меряется по сигнатуре — работают и `(other_id)`, и `(other_id, force)`. Другой объект по `other_id` резолвите только при нужде; дешевле считать по id.

## 4. Play и проверка

- Жмём `Play`: мяч падает, ложится на коллайдер земли, WASD толкает его, `Space` подбрасывает, каждый новый контакт пишет в лог строку `hit ... force ...` с id другой сущности.
- `Is Kinematic` на мяче замораживает движение от солвера (движение из скрипта остаётся); `Is Trigger` у коллайдера сообщает о контактах, не блокируя. Оба — живые чекбоксы инспектора.
- Типовые сбои: мяч проваливается — нет коллайдера либо включён `Is Trigger` без обработки контактов; клавиши не двигают — скрипт висит не на той сущности или `body` равен `None` (проверьте, что поле `Script` указывает на `ball.py`).

Дальше: GUI-меню, префабы, аудио, сборка — виджеты, переиспользование `.zpep`, позиционное аудио, запуск и поставка сцены.
