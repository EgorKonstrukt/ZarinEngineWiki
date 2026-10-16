# GUI Menu, Prefabs, Audio, Build

Four independent mini-tutorials sharing one scene. Each is complete on its own.

## A. GUI menu (Button + Label)

GUI widgets are ECS components rendered by the `GuiCanvas` overlay; widget events propagate as Python signals and the widgets work both in-editor and at runtime.

1. Create entity `Menu`. `Add Component` → `ButtonComponent`: Inspector shows `widget_text` (default `"Button"`); set it to `"Start"`.
2. Create entity `Score`. `Add Component` → `LabelComponent`: `widget_text` (default `"Label"`) plus `H Align` (`left`/`center`/`right`); set text to `"Score: 0"`.
3. Attach this script to `Score` (`Project → Create → Python Script` → `score.py`, `Add Component → Scripts`):

```python
class Score:
    points: int = 0

    def on_awake(self):
        self.label = self._entity.get_component_by_name("LabelComponent")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.SPACE):
            self.points = self.points + 1
            if self.label:
                self.label.widget_text = f"Score: {self.points}"
```

4. Press `Play`, hit `Space`: the label updates in the overlay. The pattern generalizes — any component field reachable via `get_component_by_name` can be driven from a script.

## B. Prefabs (`.zpep` reuse)

1. Select a finished entity subtree (e.g. the `Ball` with its `Rigidbody` + `SphereCollider` + script). Save it as `.zpep` (Zarin Prefab) — the prefab file stores the subtree with stable GUIDs.
2. Instantiate the `.zpep` into any scene: the engine remaps GUIDs per instance so references inside the subtree stay intact.
3. Per-instance edits (Inspector values changed after instantiation) are serialized as overrides alongside the prefab GUID — the prefab file itself is untouched.
4. Prefab-backed entities carry `prefab_guid` / `prefab_source_id` in the scene JSON; deleting those keys turns the instance into a plain local copy.

## C. Audio (AudioSource + listener)

1. Create entity `Music`. `Add Component` → `AudioSource`. Real Inspector fields: `Clip` (`clip_path`, audio file filter `*.wav *.mp3 *.ogg *.flac`), `Volume` 0–1, `Pitch` −3–3, `Loop`, `Play On Awake`, `Spatial Blend` 0–1, `Zone Shape` (sphere/box), `Min/Max Distance`, box inner/outer sizes, `Volume Rolloff` curve, `Offset`, fade in/out times.
2. Set `Clip` to a project audio file, `Loop` on, `Play On Awake` on — or trigger from a script:

```python
class Dj:
    def on_awake(self):
        self.src = self._entity.get_component_by_name("AudioSource")

    def on_update(self, dt):
        if Input.GetKeyDown(KeyCode.M):
            if self.src:
                if self.src.is_playing:
                    self.src.stop()
                else:
                    self.src.play()
```

3. Positional audio needs ears: add the `AudioListener` component to the camera entity. `play()` / `stop()` / `is_playing` are the runtime API; `stop()` respects `Fade Out Time` when set. Edge case: `AudioSource.on_enable` auto-plays whenever `clip_path` is set — even without `Play On Awake` — so re-enabling the entity restarts the clip.
4. Press `Play`: with `Play On Awake` the clip starts immediately; `M` toggles it. 3D attenuation follows `Min/Max Distance` and the rolloff curve.

## D. Run and ship

- Run a scene without the editor: `python player.py SampleScene.zpes` (any `.zpes` path works; the default scene constant is `"SampleScene.zpes"`).
- Ship a standalone executable: `python build_nuitka.py` (Nuitka-based; build settings live in `BuildSettings.json`). The player writes diagnostics to `player_diag.txt` and crash reports to `crash_logs/`.
- Scene files stay portable: `script_path` values and material/texture references are stored relative to the project root.
