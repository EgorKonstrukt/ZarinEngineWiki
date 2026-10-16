# Curve, Gradient, Color Editors

> Inline property editors: `CurveWidget`/`CurveEditorDialog` (`editor/curve/`), `GradientEditorDialog`/`GradientLineEdit` (`editor/gradient/`), color widgets (`editor/color/`), plus the split/cascade gradient hosts.

## API / Properties

| Member | Signature | Notes |
|---|---|---|
| `CurveWidget` | `(parent=None)` + `set_curve(curve: Curve)`, `get_curve() -> Curve`, `fit_view()` | `curve/editor.py`; mixes `CurveViewMixin` + `CurveKeysMixin` |
| `CurveEditorDialog` | `(curve: Curve, title="Curve Editor", parent=None)` + `get_curve() -> Curve` | Full dialog: canvas, presets, spinners |
| `CurveEditorDialog` editing | `_add_key_at(t, v)`, `_apply_preset(name)`, `_sync_spins()`, `_on_spin_changed()`, `_on_tangent_mode_changed(idx)` | Key add, preset apply, tangent modes (`TangentMode`) |
| `CurvePreview` | `(parent=None)` + `set_curve(curve)` | `curve/view.py` read-only thumbnail |
| Curve presets | `editor/curve_presets.py` registry via `@_reg(name)` | Verified names: `Linear`, `Smoothstep`, `Smootherstep`; builders also cover flat holds, steps, ease-in/out/in-out (sine, quad, cubic, quart, quint, expo, circ) |
| `GradientEditorDialog` | `(stops=None, title="Gradient Editor", parent=None)` + `get_stops()` | `gradient/dialogs.py`; add/remove/reorder stops |
| `GradientLineEdit` | `(stops=None, parent=None)` + `set_stops/get_stops`, `_open_editor()` | Inline field that opens the dialog |
| `_GradientBar` | `set_stops(stops)`, `selected_index()` | Draggable stop bar |
| Gradient sampling | `_sample_gradient(stops, t)`, `_default_gradient()` in `gradient/sampling.py` | Runtime evaluation used by previews |
| `GradientPreview` | `(parent=None)` + `set_stops(stops)` | `gradient/widgets.py` thumbnail |
| Color | `color/wheel.py`, `color/palette.py`, `color/eyedropper.py` | Wheel, swatch palette, screen picker |
| Hosts | `editor/split_gradient_widget.py`, `editor/cascade_split_widget.py` | Split/cascade layouts embedding the gradient bar |

## Usage in Editor

Curve fields open `CurveEditorDialog`: left-drag moves keys, double-click adds a key, wheel zooms, right-click menu sets the tangent mode, preset strip applies `Linear`/`Smoothstep`/`Smootherstep`/eases. Gradient fields open `GradientEditorDialog`: click the bar to add a stop, double-click edits its color, rows edit position + RGBA. Color fields offer wheel + palette + eyedropper. All edits apply live to the preview widgets.

## Usage in Code

```python
from editor.curve.editor import CurveEditorDialog
from editor.gradient.dialogs import GradientEditorDialog

dlg = CurveEditorDialog(curve, title="Falloff", parent=mw)
if dlg.exec():
    curve = dlg.get_curve()

g = GradientEditorDialog(stops=[(0.0, (1, 1, 1, 1)), (1.0, (0, 0, 0, 1))], parent=mw)
if g.exec():
    stops = g.get_stops()
```

## Related

- Inspector (curve/gradient/color field types), Shader Graph (gradient/color nodes), Terrain Graph (height bias/remap curves)
- Particle System force fields, Animation blending (curve-driven weights)

## Verified against

- `editor/curve/editor.py`, `editor/curve/view.py`, `editor/curve/keys.py`, `editor/curve_presets.py`
- `editor/gradient/dialogs.py`, `editor/gradient/widgets.py`, `editor/gradient/sampling.py`
- `editor/color/wheel.py`, `editor/color/palette.py`, `editor/color/eyedropper.py`
- `editor/split_gradient_widget.py`, `editor/cascade_split_widget.py`
