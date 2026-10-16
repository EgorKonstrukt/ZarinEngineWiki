# Настройка VR-сессии (VRPlugin)

Стерео-VR: OpenXR-сессия, сущности XR-рига и док управления в редакторе.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `VRPlugin` | `1.0.0` | `True` |

Объявлен в `plugins/vr_plugin/plug/plugin.py` (`VRPluginBase`, `DESCRIPTION = "VR display integration with OpenXR"`). Фабрика `get_plugin()` — в `plugins/vr_plugin/plug/__init__.py`.

## Файлы

- `plugins/vr_plugin/plug/plugin.py` — `VRPluginBase`: регистрация дока (`"VR Control"`, область `bottom`), `toggle_vr()`, `spawn_xr_rig(engine)`, `ensure_vr_entities()` / `remove_vr_entities()`, хуки `on_scene_loaded` / `on_viewport_ready`.
- `plugins/vr_plugin/components/xr_rig.py` — `XRRig`, `XRTrackedPoseDriver`, `XRController`, `XRHand`, `XRCull`.
- `plugins/vr_plugin/components/xr_locomotion.py` — `XRSmoothMoveProvider`, `XRSnapTurnProvider`, `XRTeleportationProvider`.
- `plugins/vr_plugin/components/xr_haptics.py` — `XRHaptics`.
- `plugins/vr_plugin/components/xr_ar.py` — `ARSession`, `ARCameraBackground`, `ARPlaneManager`, `ARRaycastManager`, `ARAnchorManager`, `ARPointCloudManager`.
- `plugins/vr_plugin/components/xrinter/` — `XRInteractionManager`, `XRRayInteractor`, `XRDirectInteractor`, `XRPokeInteractor`, `XRGrabInteractable`.
- `plugins/vr_plugin/vr_dock.py` — панель редактора `VRControlWidget`.
- `plugins/vr_plugin/core/` — рантайм: `session.py` / `session2.py` (сессия OpenXR), `state.py` / `state2.py` (состояние, `is_available()`), `swapchain.py` (кадровые буферы глаз), `controllers.py` (состояние контроллеров, хаптика).

## API

Дефолты `XRRig` (`xr_rig.py`): `move_speed = 5.0`, `turn_speed = 80.0`, `vert_speed = 2.5`, `acceleration = 12.0`, `damping = 8.0`, `ipd = 0.063`, `tracking_origin_mode = "Floor"`, `enable_locomotion = True`. `XRTrackedPoseDriver.pose_source`: `Head, CenterEye, LeftEye, RightEye, LeftHand, RightHand` (дефолт `Head`). `XRController(controller_hand)` — `"Left"` / `"Right"`, методы `SendHapticImpulse(amplitude, duration, frequency)` / `CancelHaptic()`.

Функции ядра (`core/__init__.py`, `state.py`): `is_available()`, `is_active()`, `session_running()`, `get_ipd()` / `set_ipd()` / `reset_ipd()`, `reset_hmd_origin()`, `is_eye_view()` / `set_eye_view()` / `toggle_eye_view()`, `get_controllers()`, `get_hmd_pos_offset()`. Дефолты глаз: текстуры `1440x1600`, FOV 90° по вертикали / 100° по горизонтали.

## Требования

Да, нужен OpenXR-рантайм. `is_available()` возвращает `_XR_AVAILABLE` — `True` только если удался `import xr` (`core/state.py`). Кнопка дока `Initialize VR` неактивна (`setEnabled(avail)`) пока его нет, а `session.initialize(ctx)` без него отказывается стартовать. Без рабочего OpenXR-рантайма на машине VR остаётся `Off`, сцена идёт с десктопной камеры.

## Пример настройки сессии

```python
# 1. Откройте док "VR Control" (нижняя область), нажмите "Initialize VR".
#    Статус: Off -> Initializing... -> Active (таймер обновления 333 мс).
# 2. Заспавньте риг из кода (тот же вызов, что у кнопки "Spawn XR Rig"):
rig = plugin.spawn_xr_rig(engine)
# Создаётся: XRRig (+ XRRig, XRInteractionManager, провайдеры move/snap/teleport)
#   + XRHMD (tracked pose driver, высота глаз 1.6 м)
#   + XRControllerLeft / XRControllerRight (pose driver + XRController + XRHand)
# 3. Настройте в доке: слайдер IPD 10..200 мм (дефолт 63 -> 0.063 м),
#    "Reset HMD Origin", переключатель "View: Eye/Desktop".
from plugins.vr_plugin import core as vr_core
vr_core.set_ipd(0.063)
vr_core.set_eye_view(True)   # стерео глаз vs зеркало Desktop
```

При активной сессии `on_scene_loaded` спавнит риг автоматически; при выключенном VR сущности рига удаляются из сцены.

## Проверено по коду

`plugins/vr_plugin/plug/plugin.py`, `plugins/vr_plugin/plug/__init__.py`, `plugins/vr_plugin/vr_dock.py`, `plugins/vr_plugin/components/xr_rig.py`, `plugins/vr_plugin/components/xr_locomotion.py`, `plugins/vr_plugin/components/xr_haptics.py`, `plugins/vr_plugin/components/xr_ar.py`, `plugins/vr_plugin/core/__init__.py`, `plugins/vr_plugin/core/state.py`, `plugins/vr_plugin/core/session.py`.
