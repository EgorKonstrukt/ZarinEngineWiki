# Примеры скриптов

Готовые поведения для копирования. Все запускаются как есть: создайте ассет Python Script, замените содержимое, повесьте на сущность, нажмите Play.

Rotator — вращение вокруг Y на дельту кадра:

```python
from core.maths.math3d import Vec3


class Rotator:
    speed: float = 90.0

    def on_update(self, dt):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

Mover — равномерное движение вперёд со слайдером в инспекторе:

```python
from typing import Annotated
from core.maths.math3d import Vec3


class Mover:
    speed: Annotated[float, Range(0.0, 20.0)] = 5.0

    def on_update(self, dt):
        t = self._entity.transform
        if t:
            t.translate(Vec3(0.0, 0.0, self.speed * dt))
```

Driver — движение на WASD плюс спринт на Shift:

```python
from core.maths.math3d import Vec3


class Driver:
    speed: float = 6.0
    sprint: float = 10.0

    def on_update(self, dt):
        v = Vec3(0.0, 0.0, 0.0)
        if Input.GetKey(KeyCode.W):
            v = v + Vec3(0.0, 0.0, 1.0)
        if Input.GetKey(KeyCode.S):
            v = v + Vec3(0.0, 0.0, -1.0)
        if Input.GetKey(KeyCode.A):
            v = v + Vec3(-1.0, 0.0, 0.0)
        if Input.GetKey(KeyCode.D):
            v = v + Vec3(1.0, 0.0, 0.0)
        rate = self.sprint
        if not Input.GetKey(KeyCode.Shift):
            rate = self.speed
        t = self._entity.transform
        if t and v.length() > 0.0:
            t.translate(v.normalized() * rate * dt)
```

Patrol — пинг-понг маршрута с enum-режимом и кэшированным трансформом:

```python
from enum import Enum
from core.maths.math3d import Vec3


class PatrolMode(Enum):
    PINGPONG = 0
    LOOP = 1


class Patrol:
    mode: PatrolMode = PatrolMode.PINGPONG
    speed: float = 3.0
    distance: float = 8.0

    def on_awake(self):
        self.t = self._entity.transform
        self.home = Vec3(0.0, 0.0, 0.0)
        self.dir = 1.0
        if self.t:
            self.home = self.t.position

    def on_update(self, dt):
        if not self.t:
            return
        step = self.speed * self.dir * dt
        self.t.translate(Vec3(step, 0.0, 0.0))
        walked = self.t.position.x - self.home.x
        if self.mode == PatrolMode.LOOP:
            if walked > self.distance or walked < 0.0:
                self.t.position = Vec3(self.home.x, self.t.position.y, self.t.position.z)
        else:
            if walked > self.distance or walked < 0.0:
                self.dir = -self.dir
```

Follower — преследование Entity из пикера инспектора:

```python
from core.maths.math3d import Vec3


class Follower:
    target: 'Entity' = None
    speed: float = 4.0
    stop_at: float = 1.5

    def on_update(self, dt):
        if not self.target:
            return
        t = self._entity.transform
        goal = self.target.transform
        if not t or not goal:
            return
        delta = goal.position - t.position
        dist = delta.length()
        if dist > self.stop_at:
            t.translate(delta.normalized() * self.speed * dt)
```

HitCounter — колбэки столкновений с силой удара и кнопкой инспектора:

```python
class HitCounter:
    hits: int = 0
    last_force: float = 0.0
    _inspector_buttons = [("reset", "Reset")]

    def on_collision_enter(self, other_id, force):
        self.hits = self.hits + 1
        self.last_force = force

    def reset(self):
        self.hits = 0
        self.last_force = 0.0
```

Kicker — импульс по пробелу для сущности с Rigidbody:

```python
from core.maths.math3d import Vec3


class Kicker:
    impulse: float = 5.0

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.Space):
            rb = self._entity.get_component_by_name("Rigidbody")
            if rb:
                rb.add_impulse(Vec3(0.0, self.impulse, 0.0))
```

Spawner — таймерная логика с безопасным enable/disable:

```python
class Spawner:
    interval: float = 2.0
    active: bool = True

    def on_awake(self):
        self.clock = 0.0
        self.count = 0

    def on_update(self, dt):
        if not self.active:
            return
        self.clock = self.clock + dt
        if self.clock >= self.interval:
            self.clock = 0.0
            self.count = self.count + 1
            Logger.info("spawn tick")

    def on_disable(self):
        self.clock = 0.0
```
