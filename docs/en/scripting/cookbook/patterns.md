# Scripting Patterns Cookbook

Copy-paste patterns built only on verified engine APIs: `Scene.create_entity` / `remove_entity` / `get_entity` / `get_entity_by_name` / `get_all_entities` (`core/ecs/scene.py`), `Entity.get_component_by_name` / `set_parent` (`core/ecs/entity.py`), `Transform.translate(Vec3)` / `rotate(Vec3 euler degrees)` (`core/components/transform/tr/matrix.py`), `Engine.instance().time_scale` (`core/engine/eng/loop.py`), `GuiApi` widgets + `GuiCanvas` (`core/gui/api.py`), stdlib `json`.

## 1. Script-to-script communication through the entity

Scripts see each other via the shared `Entity` (`self._entity` is assigned by the script component, `core/components/scripting/scriptc/fields.py`). No imports of the other script needed.

```python
class Health:
    hp: float = 100.0

    def damage(self, amount):
        self.hp = max(0.0, self.hp - float(amount))
        if self.hp <= 0.0:
            Logger.info(f"{self._entity.name} died")


class DamageDealer:
    amount: float = 10.0
    target_name: str = "Enemy"   # entity NAME, resolved through the scene

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.F):
            scene = self._entity._scene
            target = scene.get_entity_by_name(self.target_name)
            if target is None:
                return
            hp = target.get_component_by_name("Health")  # script components resolve by class name
            if hp is not None and hasattr(hp, "_py_instance") and hp._py_instance is not None:
                hp._py_instance.damage(self.amount)
```

Same-entity variant: `self._entity.get_component_by_name("Health")`.

## 2. Timers by accumulating `dt`

`on_update(dt)` receives the scaled frame delta; accumulate it — pausing via `time_scale` then freezes timers for free.

```python
class Blinker:
    interval: float = 1.0
    duration: float = 5.0

    def on_start(self):
        self._t = 0.0
        self._life = 0.0
        self._on = True

    def on_update(self, dt):
        self._t += dt
        self._life += dt
        if self._t >= self.interval:
            self._t = 0.0
            self._on = not self._on
            mr = self._entity.get_component_by_name("MeshRenderer")
            if mr is not None:
                mr.enabled = self._on
        if self._life >= self.duration:
            self._entity._scene.remove_entity(self._entity.id)
```

## 3. Object pooling

Pre-create with `scene.create_entity(name)`, park the idle ones far away, recycle by repositioning. Uses only `create_entity` / `get_entity` / `local_position`.

```python
class BulletPool:
    size: int = 20
    speed: float = 20.0

    def on_start(self):
        from core.maths.math3d import Vec3  # injected anyway; import is harmless
        scene = self._entity._scene
        self._free = []
        self._busy = {}  # entity id -> travelled
        for i in range(self.size):
            e = scene.create_entity(f"PooledBullet_{i}")
            e.transform.local_position = Vec3(0.0, -9999.0, 0.0)
            self._free.append(e.id)

    def fire(self, origin):
        if not self._free:
            return
        scene = self._entity._scene
        e = scene.get_entity(self._free.pop(0))
        e.transform.local_position = Vec3(origin.x, origin.y, origin.z)
        self._busy[e.id] = 0.0

    def on_update(self, dt):
        scene = self._entity._scene
        for eid in list(self._busy):
            e = scene.get_entity(eid)
            step = self.speed * dt
            e.transform.translate(Vec3(0.0, 0.0, step))
            self._busy[eid] += step
            if self._busy[eid] > 100.0:  # recycle
                e.transform.local_position = Vec3(0.0, -9999.0, 0.0)
                del self._busy[eid]
                self._free.append(eid)
```

## 4. Save / load via JSON (GUI file picker optional)

Persistence is plain `json` + `Transform.local_position` / `local_rotation`. `GuiApi.input()` (`core/gui/api.py`) can supply the filename widget on a `GuiCanvas`.

```python
import json
import os

class SavePoint:
    slot: str = "assets/saves/slot1.json"

    def _data(self):
        scene = self._entity._scene
        out = {}
        for e in scene.get_all_entities():
            t = e.get_component_by_name("Transform")
            if t is not None:
                out[e.name] = {
                    "pos": t.local_position.to_list(),
                    "rot": t.local_rotation.to_list(),
                }
        return out

    def save(self):
        os.makedirs(os.path.dirname(self.slot), exist_ok=True)
        with open(self.slot, "w", encoding="utf-8") as f:
            json.dump(self._data(), f, indent=2)

    def load(self):
        from core.maths.math3d import Vec3, Quat
        with open(self.slot, "r", encoding="utf-8") as f:
            data = json.load(f)
        scene = self._entity._scene
        for name, rec in data.items():
            e = scene.get_entity_by_name(name)
            if e is None:
                continue
            t = e.get_component_by_name("Transform")
            if t is not None:
                p, r = rec["pos"], rec["rot"]
                t.local_position = Vec3(p[0], p[1], p[2])
                t.local_rotation = Quat(r[0], r[1], r[2], r[3])

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.F5):
            self.save()
        if Input.GetKeyDown(KeyCode.F9):
            self.load()
```

## 5. Pause via `time_scale`

`Engine.time_scale` (`loop.py`) clamps at `0.0`; the loop multiplies the frame delta (`dt = raw_dt * self._time_scale`), so `0.0` freezes `on_update(dt)` and physics while input polling continues.

```python
class PauseToggle:
    def on_update(self, dt):
        from core.engine.eng import Engine
        if Input.GetKeyDown(KeyCode.P):
            eng = Engine.instance()
            eng.time_scale = 0.0 if eng.time_scale > 0.001 else 1.0
            Logger.info(f"time_scale={eng.time_scale}")
```

## Verified against

`core/ecs/scene.py`, `core/ecs/entity.py`, `core/components/transform/tr/matrix.py`, `core/components/scripting/scriptc/fields.py`, `core/components/scripting/scriptc/component.py`, `core/engine/eng/loop.py`, `core/gui/api.py`, `core/maths/math3d.py`.
