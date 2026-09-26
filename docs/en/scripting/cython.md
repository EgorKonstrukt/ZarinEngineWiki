# Cython in Scripts

Heavy computations move to a Cython module next to the plain script — no manual build steps, no polluted script folders.

Setup:

1. `Project → Create → Cython Script` creates `fast_sum.pyx` with a working template.
2. A plain script in the same folder writes `import fast_sum`.
3. Before import, the engine prepares the script folder on the module search path, then on first import compiles the extension into `cache/cython` automatically. On Windows the bundled MinGW toolchain is discovered by the engine itself.

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

Rules and mechanics:

- The module name is top-level: `import foo` where `foo.pyx` sits next to the script. Names must be unique within the project. A neighboring `foo.pxd` declaration file is tracked for rebuilds too.
- Rebuilds are automatic when the `.pyx` (or `.pxd`) is newer than the cached build. Artifacts live in `cache/cython`; the script folder stays clean. Editing `.pyx` during Play triggers a script hot-reload as usual.
- The `Check` button validates `.pyx` without a C compiler (Cython parsing and typing only). The full native build happens lazily on first import.
- If no compiler is found, the console shows an actionable error and the script keeps working on the previous working build — iteration never hard-blocks on toolchain setup.
- Keep the Python/Cython boundary typed (`cpdef`, typed locals) and batch calls: one call processing an array beats per-element calls across the boundary.

When to reach for it: per-frame math over many elements (crowds, particles logic, procedural deformation), tight loops the profiler attributes to a script, and numeric kernels. Game flow, input and orchestration stay in plain Python.

Troubleshooting: stale results → touch the `.pyx` (mtime drives rebuilds); import errors → module name collision within the project, rename uniquely; slow first Play → that is the one-time compile, later runs reuse `cache/cython`.
