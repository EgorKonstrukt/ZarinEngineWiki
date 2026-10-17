# Timers and Delayed Actions

Honest summary first: the scripting runtime has **no coroutines, no `Invoke`, no `InvokeRepeating`, no `StartCoroutine`, no scheduler**. The dispatcher (`_call_py`) only invokes lifecycle methods; nothing in `core/components/scripting`, `core/ecs`, or `core/input` implements delayed calls (the only `invoke_rpc` in the codebase is the network RPC path, and the only coroutines are asyncio internals of network transports). The single code-backed timing path is **accumulating `dt` in `on_update`/`on_fixed_update`**.

## The pattern

```python
def on_start(self):
    self.timer = 0.0

def on_update(self, dt):
    self.timer += dt
    if self.timer >= self.delay:
        self.timer = 0.0   # or self.timer -= self.delay to avoid drift
        self.fire()
```

Use `on_update` for UI/countdown pacing and `on_fixed_update` for physics-adjacent timing. Pause behavior: render `dt` is already time-scaled (0 while paused), so `on_update` timers freeze with the game for free.

## Cooldown example (full, runnable)

```python
class CooldownShot:
    cooldown: float = 0.5

    def on_start(self):
        self.ready_in = 0.0

    def on_update(self, dt):
        if self.ready_in > 0.0:
            self.ready_in -= dt
        if Input.GetMouseButtonDown(0) and self.ready_in <= 0.0:
            self.ready_in = float(self.cooldown)
            Logger.info("bang")
```

## Respawner example (full, runnable)

```python
class Respawner:
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

## Wave spawner example (full, runnable)

```python
from core.components.transform.tr import Transform
from core.maths.math3d import Vec3


class WaveSpawner:
    wave_gap: float = 10.0
    per_wave: int = 4

    def on_start(self):
        self.scene = self._entity._scene
        self.clock = 0.0
        self.wave = 0

    def on_update(self, dt):
        self.clock += dt
        if self.clock >= float(self.wave_gap):
            self.clock -= float(self.wave_gap)
            self.wave += 1
            for i in range(int(self.per_wave)):
                e = self.scene.create_entity(f"Wave{self.wave}_{i}")
                t = Transform()
                t.local_position = Vec3(float(i) * 2.0, 0.0, float(self.wave) * 5.0)
                e.add_component(t)
            Logger.info(f"Wave {self.wave} spawned")
```

Repeating-pulse variant without drift (`timer -= interval` keeps long-run cadence even under hitches).

## Related

- `lifecycle.md` — `dt` semantics (scaled render dt vs stable fixed dt).
- `entity-api.md` — activating/deactivating entities from timers.
- `network-scripting.md` — throttle `send_rpc` with a cooldown instead of per-frame sends.

## Verified against

- `core/components/scripting/scriptc/hotreload.py:18-36` (complete lifecycle/alias table — no timer/scheduler entries), `:253-260` (`_call_py` only dispatches these).
- Codebase search: no `StartCoroutine`/`InvokeRepeating`/`Invoke(` scheduling in scripting/ECS/input paths (`invoke_rpc` is network-only, `core/network/rpc.py:33-69`); `coroutine` hits are asyncio transport internals.
