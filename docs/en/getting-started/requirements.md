# Requirements

> What ZarinEngine needs to run: Python 3.13+, PySide6, ModernGL, NumPy, OpenAL, Assimp, and OpenGL 4.6.

[Home](../../index.md) | [Section index](./_section.md)

## Overview

The engine is pure Python plus native libraries for windowing, rendering, audio, and mesh import. No C++ toolchain is needed for normal use (Cython extensions build automatically on first editor start via `setup.py build_ext --inplace`).

## Properties

| name | type | default | description |
|---|---|---|---|
| python | version | `3.13+` | Interpreter version (`README.md`) |
| PySide6 | package | `>= 6.6.0` | Editor UI, viewport windowing |
| ModernGL | package | `>= 5.10.0` | OpenGL rendering backend |
| NumPy | package | `>= 1.26.0` | All math is `numpy.float64`; `float32` only on GPU upload |
| OpenAL | system library | — | 3D positional audio (`pyopenal`, `miniaudio`) |
| Assimp | bundled binary | — | `assimp-vc143-mt.dll` / `libassimp.so.6.0.5` for mesh import |
| OpenGL | driver feature | `4.6 Core Profile` | Requested in `main.py` via `QSurfaceFormat` (depth buffer 24) |
| full list | file | `requirements.txt` | `PySide6, moderngl, numpy, pyopenal, miniaudio, pynput, msgpack, websockets, cython, setuptools, nuitka, pillow, imageio, qtawesome, xxhash, psutil, culverin, pyyaml` |

## Usage in Editor

Not applicable. Requirements are checked before the editor starts: if native extensions are missing or stale, `main.py` rebuilds them with a splash-screen progress bar.

## Usage in Code

```python
# From the engine root:
#   pip install -r requirements.txt
#   python main.py
```

## Serialization

Not applicable.

## Related pages

- [Installation](./installation.md)
- [Quick Start](./quick-start.md)
- [FAQ](./faq.md)
- [Configuration](../core/config.md)

## Verified against

- `README.md` (Requirements section)
- `requirements.txt` (full package list)
- `main.py` (`QSurfaceFormat` 4.6 Core, `_check_extensions_missing`, `_build_extensions_with_splash`)
- `core/maths/math3d.py` (`FLOAT_TYPE = np.float64`)
