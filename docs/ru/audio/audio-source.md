# AudioSource

Играющий голос на сущности: клип, громкость, питч, зацикливание, 3D-позиция, спад по дистанции, фейды и колбэки финала.

| Группа / поле инспектора | Default | Смысл |
|---|---|---|
| Playback: Clip | — | Аудиофайл (wav/mp3/ogg/flac) |
| Playback: Volume | 1.0 | Множитель громкости |
| Playback: Pitch | 1.0 | Скорость проигрывания, −3…3 |
| Playback: Loop | выкл | Перезапуск в конце |
| Playback: Play On Awake | выкл | Автостарт в `on_start` |
| Spatial: Spatial Blend | 1.0 | 0 = плоское 2D, 1 = полное 3D |
| Spatial: Zone Shape | sphere | Объём затухания SPHERE или BOX |
| Spatial: Volume Rolloff | linear | Редактор кривой, ключи по умолчанию `[[0,1],[1,0]]` |
| Spatial: Min / Max Distance | 1.0 / 50.0 | Радиус полной громкости и радиус тишины |
| Spatial: Box Inner / Outer Size | 2³ / 10³ | BOX-зона: габариты полной громкости и спада |
| Fades: Offset (sec) | 0.0 | Стартовая позиция внутри клипа |
| Fades: Fade In / Fade Out Time | 0.0 | Авто-рампы на play / stop |

Жизненный цикл, сверено с кодом:

- `on_start` играет при выставленном `play_on_awake`, назначенном клипе и тишине.
- `on_enable` перезапускает клип при каждом включении компонента с назначенным клипом.
- `on_disable` / `on_destroy` немедленно останавливают нативный источник.
- `on_update` крутит активные фейды, опрашивает `AL_SOURCE_STATE`, перезапускает зацикленные клипы, стреляет `on_finished` (естественный конец) или `on_stopped` (остановлен, фейд-аут завершён). Исключения колбэков логируются, не пробрасываются.

API из скриптов:

```python
src = self._entity.get_component_by_name("AudioSource")

def on_start(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src:
        src.on_finished = self.next_track
        src.play()

def next_track(self):
    src = self._entity.get_component_by_name("AudioSource")
    if src:
        src.stop()

def on_update(self, dt):
    src = self._entity.get_component_by_name("AudioSource")
    if src and Input.GetKeyDown(KeyCode.M):
        if src.is_playing:
            src.stop()
        else:
            src.play()
```

Детали:

- `play()` — no-op без клипа или во время проигрывания; пробрасывает в менеджер громкость, питч, loop, spatial blend, дистанции, спад, offset и форму зоны, взводит `fade_in_time` через старт с нулевым гейном.
- `stop()` при выставленном `fade_out_time` сначала делает фейд-аут и только потом отпускает источник; без него остановка мгновенная. `fade_in(duration)` / `fade_out(duration)` вызываются и напрямую.
- `is_playing` отражает флаг состояния компонента.
- Пресеты спада: `linear_preset()` возвращает `[[0,1],[1,0]]`; `logarithmic_preset()` строит ключи `1/(1+2t)` с шагом 0.1 для естественного спада по дистанции.
- Оценка зоны по умолчанию сферическая (`min_distance` — полная громкость, `max_distance` — тишина); режим BOX использует inner как полную громкость и outer как границу спада. Гизмо рисует активную форму во вьюпорте.

Диагностика: нет звука — проверьте резолв пути клипа, наличие AudioSystem (установлен openal), наличие слушателя, ненулевые volume и spatial blend, слушатель внутри max distance; щелчки на стопе — задайте малый fade_out_time; щель в лупе — держите loop вкл и не дёргайте play каждый кадр.

Связанное: AudioListener, зона реверберации, аудиосистема, Curve (редактор спада).
