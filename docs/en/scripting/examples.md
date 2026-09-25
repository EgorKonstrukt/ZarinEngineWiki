# Script Examples

Copy-paste behaviors. All of them run as-is: create a Python Script asset, replace its content, attach to an entity, press Play.

Rotator — spins around Y using the frame delta:

```python
from core.maths.math3d import Vec3


class Rotator:
    speed: float = 90.0

    def on_update(self, dt):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

Mover — constant forward motion with an Inspector slider:

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

Keyboard driver — WASD movement plus sprint on Shift:

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

Hit counter — collision callbacks with impact force:

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

Physics kick — impulse on key press for an entity with Rigidbody:

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
