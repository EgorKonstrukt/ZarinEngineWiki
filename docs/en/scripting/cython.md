# Cython in Scripts

Heavy computations can move to a Cython module next to the plain script, with no manual build steps.

1. `Project → Create → Cython Script` creates `fast_sum.pyx` with a template.
2. A plain script in the same folder writes `import fast_sum`.
3. On first import the engine compiles the extension into `cache/cython` automatically (on Windows the bundled MinGW toolchain is found by the engine) and imports it.

```python
import move_fast


class Mover:
    speed: float = 5.0

    def on_update(self, dt):
        step = move_fast.clamp_step(self.speed * dt, 0.0, 1.0)
        t = self._entity.transform
        if t:
            t.translate(Vec3(0.0, 0.0, step))
```

```cython
cpdef double clamp_step(double value, double lo, double hi):
    if value < lo:
        return lo
    if value > hi:
        return hi
    return value
```

Rules:

- The module name is top-level: `import foo` where `foo.pyx` sits next to the script. Names must be unique within the project. A neighboring `foo.pxd` is also tracked for rebuilds.
- Rebuilds are automatic when the `.pyx` file is newer than the build. Artifacts live in `cache/cython`, the script folder stays clean. Editing `.pyx` during Play triggers a script hot-reload as usual.
- The `Check` button validates `.pyx` without a C compiler (Cython parsing and typing only). The full build happens on first import.
- If no compiler is found, the console shows an actionable error and the script keeps working on the previous working build.
