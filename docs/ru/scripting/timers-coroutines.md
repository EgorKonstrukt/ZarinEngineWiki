# Таймеры и отложенные действия

Честная сводка: в рантайме скриптов **нет корутин, нет `Invoke`, нет `InvokeRepeating`, нет `StartCoroutine`, нет планировщика**. Диспетчер (`_call_py`) вызывает только методы жизненного цикла; ничто в `core/components/scripting`, `core/ecs` или `core/input` отложенных вызовов не реализует (единственный `invoke_rpc` в кодовой базе — сетевой RPC-путь, а корутины — asyncio-внутренности сетевых транспортов). Единственный подтверждённый кодом путь тайминга — **накопление `dt` в `on_update`/`on_fixed_update`**.

## Паттерн

```python
def on_start(self):
    self.timer = 0.0

def on_update(self, dt):
    self.timer += dt
    if self.timer >= self.delay:
        self.timer = 0.0   # или self.timer -= self.delay, чтобы не дрейфовать
        self.fire()
```

`on_update` — для UI/отсчётов, `on_fixed_update` — для околофизического тайминга. Пауза: рендерный `dt` уже отмасштабирован временем (на паузе 0), так что таймеры `on_update` встают вместе с игрой бесплатно.

## Пример кулдауна (полный, рабочий)

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

## Пример респавнера (полный, рабочий)

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

## Пример волнового спавнера (полный, рабочий)

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

Вариант повторяющегося пульса без дрейфа (`timer -= interval` держит каденс даже при hitch'ах).

## Связанные страницы

- `lifecycle.md` — семантика `dt` (масштабированный рендерный против стабильного fixed).
- `entity-api.md` — активация/деактивация сущностей по таймерам.
- `network-scripting.md` — троттлите `send_rpc` кулдауном, а не шлите каждый кадр.

## Проверено по коду

- `core/components/scripting/scriptc/hotreload.py:18-36` (полная таблица жизненного цикла/алиасов — записей таймеров/планировщика нет), `:253-260` (`_call_py` диспетчит только их).
- Поиск по кодовой базе: `StartCoroutine`/`InvokeRepeating`/планирующего `Invoke(` в путях scripting/ECS/input нет (`invoke_rpc` — только сеть, `core/network/rpc.py:33-69`); совпадения `coroutine` — asyncio-внутренности транспортов.
