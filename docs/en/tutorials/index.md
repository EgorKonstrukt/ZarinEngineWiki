# Tutorials Overview

Three hands-on guides. Read them in order; each builds on the previous one.

Prerequisites (same for all tutorials):

1. Install dependencies (`pip install -r requirements.txt`) and open the editor (`python main.py`). No project setup is required.
2. Learn the four UI stops used everywhere: `Hierarchy` panel (scene tree) → `Inspector → Add Component` (the `Add Component` dialog) → `Inspector` field editing → `Play` to run.
3. Scripts are created with `Project → Create → Python Script` (`.py`) and attached with `Inspector → Add Component → Scripts`, picking the file in the `Script` field. Values typed in the Inspector are stored in the scene (`.zpes`), not in the `.py` file.

Guides:

- Rotator and Mover Scripts — spinning and WASD movement with `Transform.rotate` / `translate`, `Input`/`KeyCode`, Inspector-tuned `speed`. Start here.
- Physics Scene — `Rigidbody` + `BoxCollider` ground and ball, forces in `on_fixed_update`, `on_collision_enter` counting hits.
- GUI Menu, Prefabs, Audio, Build — `ButtonComponent`/`LabelComponent` menu, `.zpep` reuse, `AudioSource.play()` on keypress, running `python player.py Scene.zpes`, standalone build with `python build_nuitka.py`.

Conventions: `self._entity` is the owning entity (assigned by the engine before every call); `get_component_by_name("...")` fetches a sibling component by its registered name (`"Transform"`, `"Rigidbody"`, `"AudioSource"`, `"LabelComponent"`); rotation input is a `Vec3` of degrees applied by `Transform.rotate`, which is the only `rotate` in the scripting path — `Vec3` itself has no `rotate` method, only `dot`, `cross`, `length`, `normalized`, `lerp` and friends.
