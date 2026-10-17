# Аудио из кода

Запуск и настройка звуков из игрового кода. Компонент: `AudioSource` (`get_component_by_name("AudioSource")`).

## Поля (все живьём настраиваемые)

| Поле | По умолчанию | Замечания |
|---|---|---|
| `clip_path` | `""` | Ресурс аудио; пустой ⇒ `play()` — no-op. |
| `volume` | `1.0` | 0–1. Каждый кадр умножается на громкость фейда. |
| `pitch` | `1.0` | −3…3. |
| `loop` | `False` | Перезапуск из `on_update`, когда голос кончился. |
| `play_on_awake` | `False` | Авто-`play()` в `on_start`, если клип задан. |
| `spatial_blend` | `1.0` | 0 = 2D, 1 = 3D. |
| `zone_shape` | `SPHERE` | Enum `AudioZoneShape`. |
| `volume_rolloff` | `[[0, 1], [1, 0]]` | Точки кривой линейного спада. |
| `min_distance` / `max_distance` | `1.0` / `50.0` | Диапазон 3D-затухания. |
| `box_inner_size` / `box_outer_size` | `(2,2,2)` / `(10,10,10)` | Габариты box-зоны (`Vec3`). |
| `offset` | `0.0` | Стартовое смещение в секундах. |
| `fade_in_time` / `fade_out_time` | `0.0` | Автоматические фейды вокруг `play()`/`stop()`. |

## Методы и жизненный цикл

| Сигнатура | Поведение |
|---|---|
| `src.play()` | Старт голоса; молча возвращается без клипа или если уже играет. При `fade_in_time > 0` применяет фейд. |
| `src.stop()` | При `fade_out_time > 0` сначала затухает, затем останавливается; иначе останавливается сразу. Стреляет `on_stopped`. |
| `src.is_playing: bool` | Свойство только для чтения. |
| `src.fade_in(duration)` / `src.fade_out(duration)` | Ручные огибающие фейда. |
| `AudioSource.linear_preset()` / `AudioSource.logarithmic_preset()` | Фабрики кривых спада. |
| `src.on_finished` / `src.on_stopped` | Опциональные callable без аргументов. |

Критичное правило автостарта: `on_enable` запускает воспроизведение, **когда задан `clip_path` и ничего не играет** — независимо от `play_on_awake`. Переключение сущности в active с клипом перезапускает звук. `on_disable`/`on_destroy` останавливают голос немедленно.

## Пример шагов и выстрела (полный, рабочий)

```python
class Footsteps:
    step_gap: float = 0.35
    shot_clip: str = "assets/audio/shot.wav"

    def on_start(self):
        self.step_src = self._entity.get_component_by_name("AudioSource")
        self.timer = 0.0
        self.moving = False

    def on_update(self, dt):
        self.moving = Input.GetKey(KeyCode.W) or Input.GetKey(KeyCode.A) \
            or Input.GetKey(KeyCode.S) or Input.GetKey(KeyCode.D)
        if self.step_src is None:
            return
        if self.moving:
            self.timer += dt
            if self.timer >= float(self.step_gap):
                self.timer = 0.0
                # перезапуск шага, даже если предыдущий ещё звучит:
                self.step_src.stop()
                self.step_src.play()
        else:
            self.timer = float(self.step_gap)  # следующий шаг — сразу

        if Input.GetKeyDown(KeyCode.MOUSE_LEFT):
            self.play_one_shot(self.shot_clip, volume=0.9)

    def play_one_shot(self, clip, volume=1.0):
        scene = self._entity._scene
        e = scene.create_entity("OneShot")
        from core.components.transform.tr import Transform
        e.add_component(Transform())
        from core.ecs import ComponentRegistry
        cls = ComponentRegistry.get("AudioSource")
        voice = cls()
        voice.clip_path = clip
        voice.volume = float(volume)
        voice.spatial_blend = 0.0
        e.add_component(voice)
        voice.play()
        self._cleanup = getattr(self, "_cleanup", {})
        self._cleanup[e.id] = 2.0

    def on_fixed_update(self, dt):
        for eid in list(getattr(self, "_cleanup", {}).keys()):
            self._cleanup[eid] -= dt
            if self._cleanup[eid] <= 0.0:
                scene = self._entity._scene
                if scene.get_entity(eid) is not None:
                    scene.remove_entity(eid)
                del self._cleanup[eid]
```

О сниппете: `stop()` перед `play()` перезапускает голос; one-shot хелпер создаёт голую сущность + `Transform` + `AudioSource` только проверенными API и чистит её через 2 с. `ComponentRegistry.get("AudioSource")` возвращает зарегистрированный класс.

Громкость/питч конкретных клипов из инспектора: заведите поля `volume: float` / `pitch: float` и присвойте их источнику в `on_start` — поля источника обычные атрибуты.

## Связанные страницы

- `entity-api.md` — `get_component_by_name("AudioSource")` и кэширование в `on_start`.
- `timers-coroutines.md` — обобщение паттерна накопления `step_gap`.
- `pitfalls.md` — автостарт `on_enable`; no-op гарды `play()`.

## Проверено по коду

- `core/components/audio/source/component.py:24-76` (поля инспектора + дефолты, вкл. `volume_rolloff [[0,1],[1,0]]`), `:78-92` (автостарт `on_start`/`on_enable`, стоп при disable/destroy), `:94-104` (пресеты), `:149-198` (`play`/`stop`/`is_playing`).
