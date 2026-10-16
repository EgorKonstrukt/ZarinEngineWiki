# Указатель API

Точные пути импорта. В пользовательских скриптах `Input`, `KeyCode`, `Vec2/3/4`, `Curve`, `Range`, `Logger` уже подставлены, так что импорты ниже нужны лишь коду редактора/тулинга — объекты те же.

## Скриптинг

- `from core.ecs import Entity, Component, ComponentRegistry, Scene` — сущности, базовый класс, регистрация, сцена.
- `from core.maths.math3d import Vec2, Vec3, Vec4, Quat, Mat4` — векторы/кватернион (`float64`, Cython) и матрицы (numpy).
- `from core.input.inpsys import Input, KeyCode` — `GetKey/GetKeyDown/GetKeyUp`, `GetMouseButton*/GetButton*`, `GetAxis/GetAxisRaw`, `mousePosition`, `deltaTime`, `cursorLocked/cursorVisible`.
- `from core.foundation.curve import Curve` — ключевые кривые (`to_dict` / `from_dict` для персистентности).
- `from core.components.scripting.scriptc import ScriptComponent` — `script_path`, `hot_reload`, значения полей в `script_fields`.
- `from core.components.scripting.scriptc.fields import Range` — метаданные слайдера `Range(min_value, max_value, step)`.
- `from core.foundation.logger import Logger` — `Logger.info/warning/error`, кольцевой буфер на 2000 записей со слушателями.

Разделы: Обзор · Колбэки жизненного цикла · Поля инспектора и Range · API ввода и математики · Горячая перезагрузка · Cython в скриптах · ScriptComponent · Примеры скриптов.

## Сцены, префабы, ассеты

- Ввод-вывод сцен движка: `Engine.save_scene(path)` / `load_scene(path)` — JSON `.zpes` с релятивизованными путями и `embedded_resources`.
- Префабы: файлы-поддеревья `.zpep`; инстансы несут `prefab_guid` / `prefab_source_id` и оверрайды; реестр системных префабов — `core/prefabs/registry.py`.
- Материалы: `core.assets.matp.materials.Material` — JSON `.zpem`/`.mat` вида `{"name", "shader_path", "properties"}`.
- Шейдеры: `Shader "Name" { Properties { } SubShader { Tags { } Pass { GLSLPROGRAM ... } } }` в `core/shaders/` (`internal/`, `materials/`).
- Настройки импорта: сайдкары `<ассет>.import` в JSON (фильтр/врап/компрессия текстур, масштаб/нормали/сглаживание мешей).

Разделы: Обзор уроков · Rotator и Mover · Физическая сцена · GUI/префаб/аудио/сборка · Обзор форматов файлов.

## Рантайм-компоненты (все имена — из `Add Component`)

- Transform: `Transform` — `translate(Vec3)`, `rotate(Vec3 в градусах)`, позиция/вращение/масштаб, цепочка родителей.
- Физика 3D: `Rigidbody` (`mass`, `use_gravity`, `is_kinematic`, `add_force`, `add_torque`, `add_impulse`, `velocity`), `BoxCollider` (`center`, `size`, `is_trigger`), `SphereCollider`, `CapsuleCollider`, `MeshCollider`, `CharacterController`, `Joint`.
- Физика 2D: `Rigidbody2D`, `BoxCollider2D`, `CircleCollider2D`.
- Рендеринг: `MeshRenderer`, `SpriteRenderer`, `Camera` (перспектива/ортография, FOV, near/far), `DirectionalLight`/`Point`/`Spot`, система частиц.
- Аудио: `AudioSource` (`clip_path`, `volume`, `pitch`, `loop`, `play_on_awake`, `spatial_blend`, `play()/stop()/is_playing`), `AudioListener`, `ReverbZone`.
- GUI: `ButtonComponent` / `LabelComponent` (`widget_text`), слайдеры, вводы, дропдауны и ещё 25+, рисует оверлей `GuiCanvas`.
- Сеть: `NetworkIdentity`, `CollabServer`/`CollabClient` (TCP + msgpack), `PluginBase` (`initialize`, `shutdown`, `step`, `on_play_start/stop`, `on_scene_loaded`).

Точки входа: `python main.py` (редактор), `python player.py Scene.zpes` (игра), `python build_nuitka.py` (standalone-сборка, настройки в `BuildSettings.json`), `python setup.py` (сборка 34 Cython-расширений из `core/pyx/*.pyx`).
