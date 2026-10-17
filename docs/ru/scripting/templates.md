# Стартовые шаблоны

Шесть полных рабочих заготовок. У каждой — поля инспектора. Соглашения: `KeyCode.SPACE` (не `Space`), `Transform.rotate`/`translate`, инжектированные `Input`/`Vec3`/`Logger`, состояние инспектора — обычными аннотированными атрибутами (на диске `script_fields`). `dt` в `on_update` уже отмасштабирован временем.

## 1. Mover (WASD + прыжок на Space)

Поля: `speed: float = 6.0`, `jump: float = 8.0`. Для прыжка нужен `Rigidbody`; движение кинематическое через `translate(world_space=True)`.

```python
class Mover:
    speed: float = 6.0
    jump: float = 8.0

    def on_start(self):
        self.body = self._entity.get_component_by_name("Rigidbody")

    def on_update(self, dt):
        t = self._entity.transform
        step = float(self.speed) * float(dt)
        if Input.GetKey(KeyCode.W):
            t.translate(Vec3(0.0, 0.0, -step), world_space=True)
        if Input.GetKey(KeyCode.S):
            t.translate(Vec3(0.0, 0.0, step), world_space=True)
        if Input.GetKey(KeyCode.A):
            t.translate(Vec3(-step, 0.0, 0.0), world_space=True)
        if Input.GetKey(KeyCode.D):
            t.translate(Vec3(step, 0.0, 0.0), world_space=True)
        if Input.GetKeyDown(KeyCode.SPACE) and self.body is not None:
            self.body.add_impulse(Vec3(0.0, float(self.jump), 0.0))
```

## 2. Follower (демпфированная погоня + взгляд на цель)

Поля: `damping: float = 5.0`, `height: float = 2.0`.

```python
class Follower:
    damping: float = 5.0
    height: float = 2.0

    def on_start(self):
        self.target = self._entity._scene.get_entity_by_name("Player")

    def on_update(self, dt):
        if self.target is None or self.target.transform is None:
            return
        t = self._entity.transform
        goal = self.target.transform.position + Vec3(0.0, float(self.height), 0.0)
        k = min(1.0, float(self.damping) * float(dt))
        t.position = t.position.lerp(goal, k)
        t.look_at(self.target.transform.position)
```

## 3. Orbiter (круг вокруг пивота, градусы!)

Поля: `radius: float = 8.0`, `angular_speed: float = 30.0` (градусов/сек), `height: float = 3.0`.

```python
class Orbiter:
    radius: float = 8.0
    angular_speed: float = 30.0
    height: float = 3.0

    def on_start(self):
        import math
        self.math = math
        self.angle = 0.0
        self.pivot = self._entity._scene.get_entity_by_name("Player")

    def on_update(self, dt):
        self.angle += float(self.angular_speed) * float(dt)
        a = self.math.radians(self.angle)
        cx, cz = 0.0, 0.0
        if self.pivot is not None and self.pivot.transform is not None:
            p = self.pivot.transform.position
            cx, cz = p.x, p.z
        t = self._entity.transform
        t.position = Vec3(
            cx + float(self.radius) * self.math.cos(a),
            float(self.height),
            cz + float(self.radius) * self.math.sin(a),
        )
        t.look_at(self.pivot.transform.position if self.pivot is not None else Vec3(0.0, 0.0, 0.0))
```

## 4. Timer / Respawner (накопление dt — других таймеров нет)

Поля: `delay: float = 3.0`. Корутин нет; это санкционированная форма.

```python
class TimerRespawner:
    delay: float = 3.0

    def on_start(self):
        self.left = 0.0
        self.home = self._entity.transform.position

    def on_update(self, dt):
        if not self._entity.active:
            self.left -= dt
            if self.left <= 0.0:
                self._entity.transform.position = self.home
                self._entity.active = True

    def kill(self):
        self._entity.active = False
        self.left = float(self.delay)
```

## 5. Counter HUD (лейбл + кнопка, Qt-сигнал)

Поля: `start_score: int = 0`. Нужны сущности `ScoreLabel` (LabelComponent) и `ScoreButton` (ButtonComponent).

```python
class CounterHud:
    start_score: int = 0

    def on_start(self):
        scene = self._entity._scene
        self.score = int(self.start_score)
        self.label = scene.get_entity_by_name("ScoreLabel")
        btn_entity = scene.get_entity_by_name("ScoreButton")
        self._refresh()
        if btn_entity is not None:
            btn = btn_entity.get_component_by_name("ButtonComponent")
            w = btn._widget_ref if btn is not None else None
            if w is not None:
                try:
                    w.clicked.disconnect()
                except Exception:
                    pass
                w.clicked.connect(self._on_click)

    def _on_click(self):
        self.score += 1
        self._refresh()

    def _refresh(self):
        if self.label is None:
            return
        comp = self.label.get_component_by_name("LabelComponent")
        if comp is None:
            return
        comp.widget_text = f"Score: {self.score}"
        comp.sync_to_widget()
```

## 6. ClickSpawner (клик → сущность)

Поля: `spawn_name: str = "Pickup"`, `spawn_offset: Vec3 = Vec3(0.0, 1.0, 0.0)`. Дефолт `Vec3` сериализуется как `[x, y, z]` и проходит round-trip через `_apply_fields_to_instance`.

```python
from core.components.transform.tr import Transform


class ClickSpawner:
    spawn_name: str = "Pickup"
    spawn_offset: Vec3 = Vec3(0.0, 1.0, 0.0)

    def on_start(self):
        self.scene = self._entity._scene

    def on_update(self, dt):
        if Input.GetMouseButtonDown(0):  # индекс 0 == MOUSE_LEFT
            e = self.scene.create_entity(str(self.spawn_name))
            t = Transform()
            base = self._entity.transform.position
            off = self.spawn_offset
            if isinstance(off, (list, tuple)):
                off = Vec3(float(off[0]), float(off[1]), float(off[2]))
            t.local_position = Vec3(base.x + off.x, base.y + off.y, base.z + off.z)
            e.add_component(t)
            Logger.info(f"Spawned {e.name} at {t.local_position.to_list()}")
```

Гард `isinstance` покрывает десериализованную форму-список полей `Vec3` до их конвертации движком (см. `pitfalls.md`: `_field_values` против атрибутов инстанса).

## Связанные страницы

- `entity-api.md`, `transform-scripting.md`, `audio-scripting.md`, `gui-scripting.md`, `physics-scripting.md` — API за каждым шаблоном.
- `input-reference.md` — все использованные коды клавиш/мыши.
- `timers-coroutines.md` — почему шаблон 4 выглядит именно так.
- `pitfalls.md` — что ломается при импровизации поверх этих.

## Проверено по коду

- Те же источники, что у тематических страниц; каждый шаблон проверен вызов за вызовом: имена `KeyCode.*` есть в `core/input/inpsys/keys.py:9-134`; `Transform.translate/rotate/look_at/position` в `core/components/transform/tr/matrix.py:227-390`; `Vec3.lerp/to_list/zero` в поверхности `Vec3` (`.rotate` отсутствует); гард `add_impulse(Vec3)` в `core/components/physics/rigidbody.py:65-68`; `clicked` из `Button(QPushButton)` в `core/gui/wid/buttons.py`; `widget_text` + `sync_to_widget` в `basewidget/{component,runtime}.py`; round-trip `script_fields` в `scriptc/component.py:132-150` + `fields.py:181-214`.
