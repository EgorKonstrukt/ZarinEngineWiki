# Glossary and FAQ

## Glossary

- **Entity** — scene object with UUID, name, parent/children, tags, layer, active toggle. Behavior comes only from components.
- **Component** — data + lifecycle hooks (`on_awake/on_start/on_update/on_fixed_update/on_destroy/on_enable/on_disable`) auto-discovered via `ComponentRegistry`.
- **ScriptComponent** — the component that hosts a user `.py` behavior: `script_path` + `script_fields` + `hot_reload`.
- **Behavior class** — first class in the script file declaring a lifecycle/collision/gizmo member or `_inspector_buttons`; no base class, constructed with no arguments.
- **Inspector field** — annotated class attribute rendered as a widget; values persist per instance in the scene.
- **Play** — run mode: scripts load, `on_awake`/`on_start` fire, per-frame updates run. Gizmo methods also run outside Play.
- **Hot-reload** — mtime-triggered instance recreation during Play preserving Inspector values.
- **Fixed update** — physics-clock callback with stable `dt` (`0.02` default); render `dt` is already time-scaled.
- **Prefab (`.zpep`)** — saved entity subtree with stable GUIDs; instances remap GUIDs and keep overrides.
- **Cython companion** — sibling `.pyx` auto-built into `cache/cython` on first import, rebuilt when newer.
- **Time scale** — multiplier applied to render `dt`; fixed-step `dt` stays constant.

## FAQ

- **Script does nothing on Play?** The `Script` field is empty or points at a moved file (`script_path` is project-root-relative). Re-pick it; the Check action reports `file:line` errors.
- **`Vec3.rotate` missing?** Correct — rotation lives on `Transform.rotate(Vec3 degrees)`. `Vec3` offers `dot/cross/length/normalized/lerp`, not `rotate`.
- **`core.math3d` import fails?** Import from `core.maths.math3d`. The Check button suggests exactly this.
- **Edit during Play lost state?** Expected: `__init__` state resets on reload by design; keep tunable state in Inspector fields and rebuild caches in `on_awake`/`on_start`.
- **Ball falls through the floor?** Missing collider, `Is Trigger` without contact handling, or `Is Kinematic` where solver motion is expected.
- **No sound?** `clip_path` empty/unresolvable, `AudioListener` missing from the camera entity, or `play()` called with no `AudioSystem` in a headless context.
- **First Play is slow with `.pyx`?** One-time native compile into `cache/cython`; later runs reuse it. The Check button validates `.pyx` without a C compiler.
