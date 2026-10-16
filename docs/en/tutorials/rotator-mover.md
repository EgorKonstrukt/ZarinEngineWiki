# Rotator and Mover Scripts

First Play in five steps: one entity spins from an Inspector-tuned field, another moves with WASD. Both scripts are complete and copy-paste ready.

## 1. Create the entity

- In the `Hierarchy` panel press the add button (tooltip `Create Entity`). A new entity appears in the scene tree.
- Select it and rename to `Spinner` (the entity `name` is the scene-tree label).

## 2. Create and attach the script

- In the `Project` panel use `Create → Python Script`, save as `rotator.py` in the project folder.
- With `Spinner` selected, press `Inspector → Add Component`, open the `Scripts` category, pick the component, then set its `Script` field to `rotator.py`.
- Annotated class attributes appear in the Inspector immediately; the header `Script` field shows the file display name.

## 3. Paste the Rotator

```python
class Rotator:
    speed: float = 90.0

    def on_update(self, dt):
        t = self._entity.get_component_by_name("Transform")
        if t:
            t.rotate(Vec3(0.0, self.speed * dt, 0.0))
```

Notes, all verified against the engine: `get_component_by_name` is an `Entity` method resolving by registered component name; `Transform.rotate` takes a `Vec3` of euler degrees (here: yaw at `speed` degrees/second, framerate-independent via `dt`); `Vec3`, `Input`, `KeyCode` need no imports — they are injected into the script module. Cache the lookup in `on_awake` when the script grows; per-frame lookup is fine for a first tutorial.

## 4. Tune and Play

- In the Inspector set `Speed` to `45.0`. The value is stored in the scene (`.zpes`) under `script_fields`; the `.py` default stays `90.0` for the next instance.
- Press `Play`. The engine instantiates the class with no arguments, assigns `self._entity`, calls `on_awake` then `on_start`, then `on_update(dt)` every frame.
- Edit `speed` or the file during Play: hot-reload recreates the instance next frame, keeps Inspector values, and re-runs `on_awake`/`on_start`. A broken edit logs `Script '...' has N error(s):` and keeps the last working instance.

## 5. Add movement (Mover)

- Create a second entity `Player` the same way (`Hierarchy` add → rename), create `mover.py` via `Project → Create → Python Script`, attach through `Add Component → Scripts`.

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

`Input.GetKey` is true while held, `GetKeyDown` only on the pressed frame; `KeyCode.W/A/S/D/SPACE` are engine key codes. `Transform.translate` moves in local space by default (`world_space=False`).

Next: Physics Scene — same entity/script/Play loop with `Rigidbody` forces and collision callbacks.
