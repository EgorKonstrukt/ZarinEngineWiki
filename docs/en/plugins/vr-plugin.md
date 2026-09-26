# VR Plugin

Editor and runtime virtual-reality support in `plugins/vr_plugin/`: a runtime core plus an editor dock.

- `vr_core.py` — session and device management: headset poses feed the scene cameras, controllers appear as tracked input alongside the standard Input API for scripts.
- `vr_dock.py` — editor panel: start/stop VR sessions, mirror the headset view into a viewport, device status and per-eye settings.

Workflow: open the VR dock, connect the headset, start the session — the game view renders the stereo pair while the desktop viewport keeps the editor camera for staging. Scripts read controller poses through the same Transform/Input conventions as desktop (device-specific bindings surface as buttons and axes).

Performance stance: VR doubles the render load (two eyes) — halve render_scale first, then shadow resolution, then PostFX. The profiler's per-stage timings apply per eye; budget 11 ms total, not per eye.

Scope note: desktop-first engine with a VR annex, not a VR-native engine — room-scale tracking and guardian flows depend on the OpenVR/OpenXR runtimes installed on the machine.

Troubleshooting: headset not found → runtime (SteamVR/monado) not running; black one eye → resolution scale overflow, lower render_scale; drift → tracking loss in the runtime, recenter there.

Related: Cameras (stereo pair source), Input API (controller bindings), Profiler, Render Resolution modes.
