# VR Session Setup (VRPlugin)

Stereo VR integration: OpenXR session, XR rig entities, and the editor control dock.

| NAME | VERSION | SYSTEM |
|---|---|---|
| `VRPlugin` | `1.0.0` | `True` |

Declared in `plugins/vr_plugin/plug/plugin.py` (`VRPluginBase`, `DESCRIPTION = "VR display integration with OpenXR"`). Factory `get_plugin()` lives in `plugins/vr_plugin/plug/__init__.py`.

## Files

- `plugins/vr_plugin/plug/plugin.py` — `VRPluginBase`: dock registration (`"VR Control"`, area `bottom`), `toggle_vr()`, `spawn_xr_rig(engine)`, `ensure_vr_entities()` / `remove_vr_entities()`, hooks `on_scene_loaded` / `on_viewport_ready`.
- `plugins/vr_plugin/components/xr_rig.py` — `XRRig`, `XRTrackedPoseDriver`, `XRController`, `XRHand`, `XRCull`.
- `plugins/vr_plugin/components/xr_locomotion.py` — `XRSmoothMoveProvider`, `XRSnapTurnProvider`, `XRTeleportationProvider`.
- `plugins/vr_plugin/components/xr_haptics.py` — `XRHaptics`.
- `plugins/vr_plugin/components/xr_ar.py` — `ARSession`, `ARCameraBackground`, `ARPlaneManager`, `ARRaycastManager`, `ARAnchorManager`, `ARPointCloudManager`.
- `plugins/vr_plugin/components/xrinter/` — `XRInteractionManager`, `XRRayInteractor`, `XRDirectInteractor`, `XRPokeInteractor`, `XRGrabInteractable`.
- `plugins/vr_plugin/vr_dock.py` — `VRControlWidget` editor panel.
- `plugins/vr_plugin/core/` — runtime: `session.py` / `session2.py` (OpenXR session), `state.py` / `state2.py` (state, `is_available()`), `swapchain.py` (eye framebuffers), `controllers.py` (controller state, haptics).

## API

`XRRig` defaults (`xr_rig.py`): `move_speed = 5.0`, `turn_speed = 80.0`, `vert_speed = 2.5`, `acceleration = 12.0`, `damping = 8.0`, `ipd = 0.063`, `tracking_origin_mode = "Floor"`, `enable_locomotion = True`. `XRTrackedPoseDriver.pose_source` is one of `Head, CenterEye, LeftEye, RightEye, LeftHand, RightHand` (default `Head`). `XRController(controller_hand)` is `"Left"` / `"Right"` with `SendHapticImpulse(amplitude, duration, frequency)` / `CancelHaptic()`.

Core functions (`core/__init__.py`, `state.py`): `is_available()`, `is_active()`, `session_running()`, `get_ipd()` / `set_ipd()` / `reset_ipd()`, `reset_hmd_origin()`, `is_eye_view()` / `set_eye_view()` / `toggle_eye_view()`, `get_controllers()`, `get_hmd_pos_offset()`. Eye defaults: textures `1440x1600`, vertical FOV 90 deg, horizontal FOV 100 deg.

## Requirements

Yes — an OpenXR runtime is required. `is_available()` returns `_XR_AVAILABLE`, which is `True` only when `import xr` succeeds (`core/state.py`). The dock's `Initialize VR` button stays disabled (`setEnabled(avail)`) until then, and `session.initialize(ctx)` refuses to start without it. Without a working OpenXR runtime on the machine, VR stays `Off` and the scene runs on the desktop camera.

## Session setup example

```python
# 1. Open the "VR Control" dock (bottom area) and press "Initialize VR".
#    Status flips Off -> Initializing... -> Active (333 ms refresh timer).
# 2. Spawn the rig from code (same call the dock's "Spawn XR Rig" button makes):
rig = plugin.spawn_xr_rig(engine)
# Creates: XRRig (+ XRRig, XRInteractionManager, move/snap/teleport providers)
#   + XRHMD (tracked pose driver, eye height 1.6 m)
#   + XRControllerLeft / XRControllerRight (pose driver + XRController + XRHand)
# 3. Tune in the dock: IPD slider 10..200 mm (default 63 -> 0.063 m),
#    "Reset HMD Origin", "View: Eye/Desktop" toggle.
from plugins.vr_plugin import core as vr_core
vr_core.set_ipd(0.063)
vr_core.set_eye_view(True)   # Eye stereo vs Desktop mirror
```

When the session is active, `on_scene_loaded` auto-spawns the rig; when VR is off, rig entities are removed from the scene.

## Verified against

`plugins/vr_plugin/plug/plugin.py`, `plugins/vr_plugin/plug/__init__.py`, `plugins/vr_plugin/vr_dock.py`, `plugins/vr_plugin/components/xr_rig.py`, `plugins/vr_plugin/components/xr_locomotion.py`, `plugins/vr_plugin/components/xr_haptics.py`, `plugins/vr_plugin/components/xr_ar.py`, `plugins/vr_plugin/core/__init__.py`, `plugins/vr_plugin/core/state.py`, `plugins/vr_plugin/core/session.py`.
