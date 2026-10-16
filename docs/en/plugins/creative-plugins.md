# Music, Plotter and QtQuick

Smaller bundled extensions for sound, data and UI hosting — same base class, same hooks, narrower jobs. Verified versions: `TrackerMusicPlugin` 1.0.0 `SYSTEM = False`, `PlotterPlugin` 1.2.0 `SYSTEM = False`, `QtQuickWorldPlugin` 1.0.0 `SYSTEM = True`.

Tracker Music (`plugins/tracker_music_plugin/`, `rend/` engine: `engine.py`, `engine_seq.py`, `engine_mix.py`, `engine_fx.py`, `defs.py`):

- ProTracker-style module playback (patterns, samples, BPM-relative tempo) for retro scores and demoscene vibes.
- Drop a module file, drive play/stop from hooks or scripts, mix under positional AudioSources — music bed plus 3D effects is the intended stack.
- Tempo math follows classic tracker conventions (ticks per row against BPM).

Plotter (`plugins/plotter_plugin/`):

- Curve and data plotting panels: profiler traces, animation curves, telemetry — see numbers as pictures during tuning.
- Feed it from `step()` hooks or log taps; pair with the profiler panel for frame-time forensics.

QtQuick (`plugins/qt_quick_plugin/`):

- Hosts QtQuick/QML surfaces inside the editor: animated dashboards, custom tool UIs, web-style panels without fighting the widget toolkit.
- Use for tooling chrome; gameplay UI stays in the GUI system (GuiCanvas widgets ship in builds, QtQuick does not guarantee that).

All three iterate like any user plugin (same lifecycle, same config persistence) and package into `.zplugin` for sharing.

Related: Audio System (music bed pairing), Profiler (plotter data source), GUI Overview (gameplay UI home), .zplugin Packaging.
