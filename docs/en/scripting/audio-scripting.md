# Audio Scripting

Triggering and tuning sounds from gameplay code. Component: `AudioSource` (`get_component_by_name("AudioSource")`).

## Fields (all live-tunable)

| Field | Default | Notes |
|---|---|---|
| `clip_path` | `""` | Audio resource; empty ⇒ `play()` is a no-op. |
| `volume` | `1.0` | 0–1. Multiplied by fade volume each frame. |
| `pitch` | `1.0` | −3…3. |
| `loop` | `False` | Re-plays from `on_update` when the voice ends. |
| `play_on_awake` | `False` | Auto-`play()` in `on_start` when a clip is set. |
| `spatial_blend` | `1.0` | 0 = 2D, 1 = 3D. |
| `zone_shape` | `SPHERE` | `AudioZoneShape` enum. |
| `volume_rolloff` | `[[0, 1], [1, 0]]` | Linear falloff curve points. |
| `min_distance` / `max_distance` | `1.0` / `50.0` | 3D attenuation range. |
| `box_inner_size` / `box_outer_size` | `(2,2,2)` / `(10,10,10)` | Box-zone extents (`Vec3`). |
| `offset` | `0.0` | Start offset in seconds. |
| `fade_in_time` / `fade_out_time` | `0.0` | Automatic fades around `play()`/`stop()`. |

## Methods and lifecycle

| Signature | Behavior |
|---|---|
| `src.play()` | Starts the voice; returns silently if no clip or already playing. Applies `fade_in_time` when > 0. |
| `src.stop()` | If `fade_out_time > 0`, fades out first, then stops; otherwise stops immediately. Fires `on_stopped`. |
| `src.is_playing: bool` | Read-only property. |
| `src.fade_in(duration)` / `src.fade_out(duration)` | Manual fade envelopes. |
| `AudioSource.linear_preset()` / `AudioSource.logarithmic_preset()` | Rolloff curve factories. |
| `src.on_finished` / `src.on_stopped` | Optional zero-arg callables. |

Critical auto-start rule: `on_enable` plays **whenever `clip_path` is set and nothing is playing** — independent of `play_on_awake`. Toggling an entity active with a clip restarts its sound. `on_disable`/`on_destroy` stop the voice immediately.

## Footsteps + gunshot example (full, runnable)

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
                # restart the step sound even if the previous step still rings:
                self.step_src.stop()
                self.step_src.play()
        else:
            self.timer = float(self.step_gap)  # next step is immediate

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

Notes on the snippet: `stop()` before `play()` re-triggers a looping/off voice; the one-shot helper creates a bare entity + `Transform` + `AudioSource` purely from verified APIs and cleans it up after 2 s. `ComponentRegistry.get("AudioSource")` returns the registered class.

Per-clip volume/pitch from the Inspector: expose `volume: float` / `pitch: float` script fields and assign them to the source in `on_start` — the source fields are plain attributes.

## Related

- `entity-api.md` — `get_component_by_name("AudioSource")` and caching it in `on_start`.
- `timers-coroutines.md` — the `step_gap` accumulation pattern generalized.
- `pitfalls.md` — `on_enable` auto-start; `play()` no-op guards.

## Verified against

- `core/components/audio/source/component.py:24-76` (inspector fields + defaults incl. `volume_rolloff [[0,1],[1,0]]`), `:78-92` (`on_start`/`on_enable` auto-start, disable/destroy stop), `:94-104` (presets), `:149-198` (`play`/`stop`/`is_playing`).
- `core/ecs/component.py` (`enabled` flag respected by dispatch).
