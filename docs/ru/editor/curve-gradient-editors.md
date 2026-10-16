# Редакторы кривых, градиентов, цвета

> Встроенные редакторы свойств: `CurveWidget`/`CurveEditorDialog` (`editor/curve/`), `GradientEditorDialog`/`GradientLineEdit` (`editor/gradient/`), виджеты цвета (`editor/color/`), плюс split/cascade-хосты градиента.

## API / Properties

| Член | Сигнатура | Примечания |
|---|---|---|
| `CurveWidget` | `(parent=None)` + `set_curve(curve: Curve)`, `get_curve() -> Curve`, `fit_view()` | `curve/editor.py`; миксины `CurveViewMixin` + `CurveKeysMixin` |
| `CurveEditorDialog` | `(curve: Curve, title="Curve Editor", parent=None)` + `get_curve() -> Curve` | Полный диалог: канва, пресеты, спиннеры |
| Правки `CurveEditorDialog` | `_add_key_at(t, v)`, `_apply_preset(name)`, `_sync_spins()`, `_on_spin_changed()`, `_on_tangent_mode_changed(idx)` | Добавление ключей, применение пресетов, режимы касательных (`TangentMode`) |
| `CurvePreview` | `(parent=None)` + `set_curve(curve)` | Миниатюра read-only в `curve/view.py` |
| Пресеты кривых | Реестр `editor/curve_presets.py` через `@_reg(name)` | Проверенные имена: `Linear`, `Smoothstep`, `Smootherstep`; билдеры покрывают также плоские holds, ступени, ease-in/out/in-out (sine, quad, cubic, quart, quint, expo, circ) |
| `GradientEditorDialog` | `(stops=None, title="Gradient Editor", parent=None)` + `get_stops()` | `gradient/dialogs.py`; добавление/удаление/перестановка стопов |
| `GradientLineEdit` | `(stops=None, parent=None)` + `set_stops/get_stops`, `_open_editor()` | Встроенное поле, открывающее диалог |
| `_GradientBar` | `set_stops(stops)`, `selected_index()` | Перетаскиваемая полоса стопов |
| Семплирование градиента | `_sample_gradient(stops, t)`, `_default_gradient()` в `gradient/sampling.py` | Рантайм-вычисление для превью |
| `GradientPreview` | `(parent=None)` + `set_stops(stops)` | Миниатюра в `gradient/widgets.py` |
| Цвет | `color/wheel.py`, `color/palette.py`, `color/eyedropper.py` | Колесо, палитра свотчей, экранная пипетка |
| Хосты | `editor/split_gradient_widget.py`, `editor/cascade_split_widget.py` | Split/cascade-раскладки с полосой градиента |

## Usage in Editor

Поля кривых открывают `CurveEditorDialog`: перетаскивание двигает ключи, дабл-клик добавляет ключ, колесо зумит, правое меню задаёт режим касательной, полоса пресетов применяет `Linear`/`Smoothstep`/`Smootherstep`/eases. Поля градиентов открывают `GradientEditorDialog`: клик по полосе добавляет стоп, дабл-клик правит цвет, строки правят позицию + RGBA. Поля цвета дают колесо + палитру + пипетку. Все правки живо отражаются в превью-виджетах.

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

- Инспектор (типы полей кривых/градиентов/цвета), Граф шейдеров (узлы градиента/цвета), Граф террейна (кривые height bias/remap)
- Форс-филды Particle System, смешивание анимаций (веса от кривых)

## Verified against

- `editor/curve/editor.py`, `editor/curve/view.py`, `editor/curve/keys.py`, `editor/curve_presets.py`
- `editor/gradient/dialogs.py`, `editor/gradient/widgets.py`, `editor/gradient/sampling.py`
- `editor/color/wheel.py`, `editor/color/palette.py`, `editor/color/eyedropper.py`
- `editor/split_gradient_widget.py`, `editor/cascade_split_widget.py`
