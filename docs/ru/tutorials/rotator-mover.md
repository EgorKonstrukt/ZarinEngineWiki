# Скрипты Rotator и Mover

Первый Play за пять шагов: одна сущность крутится от настраиваемого поля, вторая едет на WASD. Оба скрипта полные, копируйте целиком.

## 1. Создаём сущность

- В панели `Hierarchy` жмём кнопку добавления (подсказка `Create Entity`). В дереве сцены появляется новая сущность.
- Выбираем её и переименовываем в `Spinner` (имя сущности — метка в дереве).

## 2. Создаём и вешаем скрипт

- В панели `Project`: `Create → Python Script`, сохраняем `rotator.py` в папке проекта.
- Выбрав `Spinner`, жмём `Inspector → Add Component`, открываем категорию `Scripts`, затем указываем файл в поле `Script`.
- Аннотированные атрибуты класса сразу появляются в инспекторе; шапка `Script` показывает имя файла.

## 3. Вставляем Rotator

```python
class Rotator:
    speed: float = 90.0

    def on_update(self, dt):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

Всё проверено по движку: `get_component_by_name` — метод `Entity`, резолвит по зарегистрированному имени компонента; `Transform.rotate` принимает `Vec3` эйлеровых градусов (здесь рысканье со скоростью `speed` градусов в секунду, кадронезависимость через `dt`); `Vec3`, `Input`, `KeyCode` импортировать не нужно — они подставляются в модуль скрипта. Когда скрипт разрастётся, кэшируйте лукап в `on_awake`; для первого урока поиск каждый кадр допустим.

## 4. Настраиваем и жмём Play

- В инспекторе ставим `Speed = 45.0`. Значение ляжет в сцену (`.zpes`) в `script_fields`; дефолт `90.0` в `.py` останется для следующих инстансов.
- Жмём `Play`. Движок инстанцирует класс без аргументов, выставляет `self._entity`, вызывает `on_awake`, затем `on_start`, далее `on_update(dt)` каждый кадр.
- Правка `speed` или файла во время Play: hot-reload пересоздаст инстанс на следующем кадре, значения инспектора сохранятся, `on_awake`/`on_start` вызовутся заново. Сломанная правка пишется в консоль как `Script '...' has N error(s):`, при этом живёт последняя рабочая версия.

## 5. Добавляем движение (Mover)

- Вторая сущность `Player` тем же путём (добавление в `Hierarchy` → переименование), `mover.py` через `Project → Create → Python Script`, вешаем через `Add Component → Scripts`.

```python
class Mover:
    speed: float = 5.0

    def on_update(self, dt):
        step = self.speed * dt
        t = self._entity.get_component_by_name("Transform")
        if not t:
            return
        if Input.GetKey(KeyCode.W):
            t.translate(Vec3(0.0, 0.0, step))
        if Input.GetKey(KeyCode.S):
            t.translate(Vec3(0.0, 0.0, -step))
        if Input.GetKey(KeyCode.A):
            t.translate(Vec3(-step, 0.0, 0.0))
        if Input.GetKey(KeyCode.D):
            t.translate(Vec3(step, 0.0, 0.0))
        if Input.GetKeyDown(KeyCode.SPACE):
            Logger.info("hop")
```

`Input.GetKey` — удерживается, `GetKeyDown` — только кадр нажатия; `KeyCode.W/A/S/D/SPACE` — коды клавиш движка. `Transform.translate` двигает по умолчанию в локальных координатах (`world_space=False`).

Дальше: Физическая сцена — тот же цикл «сущность/скрипт/Play», но с силами `Rigidbody` и колбэками столкновений.
