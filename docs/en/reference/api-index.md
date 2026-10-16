# API Index

Exact import paths. In user scripts `Input`, `KeyCode`, `Vec2/3/4`, `Curve`, `Range`, `Logger` are pre-injected, so imports below are only needed for editor/tooling code — same objects either way.

## Scripting

- `from core.ecs import Entity, Component, ComponentRegistry, Scene` — entities, base class, registration, scene.
- `from core.maths.math3d import Vec2, Vec3, Vec4, Quat, Mat4` — vectors/quaternion (`float64`, Cython) and matrices (numpy).
- `from core.input.inpsys import Input, KeyCode` — `GetKey/GetKeyDown/GetKeyUp`, `GetMouseButton*/GetButton*`, `GetAxis/GetAxisRaw`, `mousePosition`, `deltaTime`, `cursorLocked/cursorVisible`.
- `from core.foundation.curve import Curve` — keyframed curves (`to_dict` / `from_dict` for persistence).
- `from core.components.scripting.scriptc import ScriptComponent` — `script_path`, `hot_reload`, field values in `script_fields`.
- `from core.components.scripting.scriptc.fields import Range` — `Range(min_value, max_value, step)` slider metadata.
- `from core.foundation.logger import Logger` — `Logger.info/warning/error`, 2000-entry ring buffer with listeners.

Sections: Overview · Lifecycle Callbacks · Inspector Fields and Range · Input and Math API · Hot Reload · Cython in Scripts · ScriptComponent · Script Examples.

## Scene, prefabs, assets

- Engine scene I/O: `Engine.save_scene(path)` / `load_scene(path)` — JSON `.zpes` with relativized paths and `embedded_resources`.
- Prefabs: `.zpep` subtree files; instances carry `prefab_guid` / `prefab_source_id` plus overrides; system-prefab registry in `core/prefabs/registry.py`.
- Materials: `core.assets.matp.materials.Material` — `.zpem`/`.mat` JSON `{"name", "shader_path", "properties"}`.
- Shaders: `Shader "Name" { Properties { } SubShader { Tags { } Pass { GLSLPROGRAM ... } } }` under `core/shaders/` (`internal/`, `materials/`).
- Import settings: `<asset>.import` JSON sidecars (texture filter/wrap/compression, mesh scale/normals/smoothing).

Sections: Tutorials Overview · Rotator and Mover · Physics Scene · GUI/Prefab/Audio/Build · File Formats Overview.

## Runtime components (all `Add Component` names)

- Transform: `Transform` — `translate(Vec3)`, `rotate(Vec3 degrees)`, position/rotation/scale, parent-chain.
- Physics 3D: `Rigidbody` (`mass`, `use_gravity`, `is_kinematic`, `add_force`, `add_torque`, `add_impulse`, `velocity`), `BoxCollider` (`center`, `size`, `is_trigger`), `SphereCollider`, `CapsuleCollider`, `MeshCollider`, `CharacterController`, `Joint`.
- Physics 2D: `Rigidbody2D`, `BoxCollider2D`, `CircleCollider2D`.
- Rendering: `MeshRenderer`, `SpriteRenderer`, `Camera` (perspective/orthographic, FOV, near/far), `DirectionalLight`/`Point`/`Spot`, particle system.
- Audio: `AudioSource` (`clip_path`, `volume`, `pitch`, `loop`, `play_on_awake`, `spatial_blend`, `play()/stop()/is_playing`), `AudioListener`, `ReverbZone`.
- GUI: `ButtonComponent` / `LabelComponent` (`widget_text`), sliders, inputs, dropdowns and 25+ more, rendered by the `GuiCanvas` overlay.
- Networking: `NetworkIdentity`, `CollabServer`/`CollabClient` (TCP + msgpack), `PluginBase` (`initialize`, `shutdown`, `step`, `on_play_start/stop`, `on_scene_loaded`).

Entry points: `python main.py` (editor), `python player.py Scene.zpes` (game), `python build_nuitka.py` (standalone build, settings in `BuildSettings.json`), `python setup.py` (compile the 34 `core/pyx/*.pyx` Cython extensions).
