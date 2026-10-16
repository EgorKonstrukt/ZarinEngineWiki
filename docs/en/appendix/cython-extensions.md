# Cython Extensions

> 35 `.pyx` sources in `core/pyx/`; 33 compiled by `core/build_cython.py:96-130` (`EXTENSIONS`, `setup.py build_ext --inplace` → `build()`). Entry functions below are the first `def/cpdef` in each file — the one-line purpose follows from them.

[Home](../../index.md)

## Build

```bash
python setup.py build_ext --inplace [--compiler=msvc]
```

`core/build_cython.py:18-63`: Windows default is auto-downloaded compact MinGW-w64 (`tools/mingwpm`), override `--compiler=msvc` / `ZARIN_COMPILER=msvc`; directives `boundscheck=False, wraparound=False, cdivision=True, infer_types=True`, `nthreads=os.cpu_count() or 4` (`build_cython.py:132-150`). Flags: MinGW `-O2 -DNDEBUG -march=x86-64 -mtune=generic`, MSVC `/O2 /fp:fast /arch:AVX2 /GL /DNDEBUG`, Linux `-O3 -ffast-math -march=native -DNDEBUG -flto -fopenmp` (`build_cython.py:65-83`). `main.py:78-108` tracks 25 module names and rebuilds via splash when a `.pyd` is missing or its `.pyx` is newer (`total_exts = 25`, `main.py:126`). Not compiled into `EXTENSIONS` (present in `core/pyx/` but absent from the list): `_shader_parse.pyx`, `_transform_soa.pyx` (no `_ext(...)` entry at `build_cython.py:96-130`).

## Module catalog

| `.pyx` → `core.*` | one line (entry fn) |
|---|---|
| `_convex_hull.pyx` → `core._convex_hull` | Convex-hull simplices over point sets (`convex_hull_simplices`) |
| `_bvh_build.pyx` → `core._bvh_build` | BVH SAH best-split search (`sah_compute_best_split`) |
| `_raytracing_data.pyx` → `core._raytracing_data` | Raytracing acceleration data build |
| `math_helpers.pyx` → `core.math_helpers` | Scalar mat4/quat/vec3 + ray-intersect helpers (`mat4_mul_fast`, `quat_slerp`, `ray_triangle_intersect`…) |
| `_math_vec.pyx` → `core._math_vec` | Vector batch math kernels |
| `_culling.pyx` → `core._culling` | CPU frustum cull, parallel cull, plane extract, group-instance cull (`cpu_frustum_cull…`, `cull_group_instances`) |
| `_transform_batch.pyx` → `core._transform_batch` | Transform batch updates |
| `_render_utils.pyx` → `core._render_utils` | Render-side utility kernels |
| `_physics_utils.pyx` → `core._physics_utils` | Physics-side utility kernels |
| `_core_batch.pyx` → `core._core_batch` | Flat-array mat4/vec3 core kernels (`mat4_mul_flat`, `rotate_around_axis_flat`, `look_at_flat`…) |
| `_types.pyx` → `core._types` | Shared C-level type definitions |
| `_ecs_batch.pyx` → `core._ecs_batch` | ECS/SOA batch updates, parent-index resolve, world-matrix write-back (`batch_update_from_transforms`, `write_world_matrices`…) |
| `_render_batch.pyx` → `core._render_batch` | Renderer batch kernels |
| `_octree_batch.pyx` → `core._octree_batch` | AABB batch tests: intersect/contain/ray/point/sphere (`aabb_*_batch`) |
| `_constraint_batch.pyx` → `core._constraint_batch` | Constraint batch solvers: weighted pos/scale/euler, follow, rotate-towards, scale-to (`batch_*`) |
| `_curve_batch.pyx` → `core._curve_batch` | Curve batch/single/multi evaluation (`evaluate_curve_batch`, `evaluate_curve_single`, `evaluate_multi_curve_batch`) |
| `_constraint_update.pyx` → `core._constraint_update` | Per-frame constraint update driver (`batch_update_constraints`) |
| `_physics_sync.pyx` → `core._physics_sync` | Physics↔ECS synchronization kernels |
| `_mesh_import.pyx` → `core._mesh_import` | Mesh-import kernels (OBJ chunk parse, smooth normals via `_cy_parse_obj_chunk`, `_cy_smooth_normals` in `importer/settings.py:16-18,107-109`) |
| `_skinning.pyx` → `core._skinning` | Skinning kernels |
| `_ik.pyx` → `core._ik` | IK solvers: `two_bone_flat`, `fabrik_flat`, `batch_update_ik` (see `tests/smoke/test_ik_numeric.py`) |
| `_physbone.pyx` → `core._physbone` | PhysBone stepper (`step_physbone`) |
| `_audio_dsp.pyx` → `core._audio_dsp_cy` | DSP ring-buffer kernels: `push_samples`, `mix_add`, `extract_last`, `analyze_spectrum` |
| `_raycast.pyx` → `core._raycast` | Raycast kernels |
| `_shadow_batch.pyx` → `core._shadow_batch` | Shadow batch kernels |
| `_scene_query.pyx` → `core._scene_query` | Scene query kernels (backing `Scene.update/fixed_update/start` via `_query` in `scene.py:355-359`) |
| `_render_collect.pyx` → `core._render_collect` | Render collection kernels |
| `_math_mat4.pyx` → `core._math_mat4` | Batched float32 mat4 ops: identity, mul, transpose, perspective, inverse (`mat4_batch_*`) |
| `_vr_batch.pyx` → `core._vr_batch` | VR batch kernels |
| `_nav_batch.pyx` → `core._nav_batch` | Navigation batch kernels |
| `_splat_sort.pyx` → `core._splat_sort` | Gaussian-splat sort kernels |
| `_gs_volume.pyx` → `core._gs_volume` | GS-volume occupancy kernels: `splat_occupancy`, `dilate26`, `greedy_merge_boxes` |
| `_sky_astro.pyx` → `core._sky_astro` | Sky/astronomy kernels |

## User-script Cython (per-script companions)

Separate from engine extensions: `core/components/scripting/cythonp/` builds a `.pyx` next to a user `.py` on first import, rebuilds on change (hot-reload compatible). Layout: `CACHE_SUBDIR = "cache/cython"` (`cythonp/build.py:12`); per-script dir `build/<sha256(script_dir)[:16]>` (`build.py:48-55`); per-version dir = source fingerprint `sha256(scheme + cython_version + compiler + pyx [+ pxd])[:12]` (`build.py:56-80`); `_KEEP_VERSIONS = 3` pruned by `_prune_versions` (`build.py:14,93-100`); template `CYTHON_TEMPLATE` (`cythonp/loader.py:22-54`, `add` + `moving_average` example). Exercised by `tests/smoke/test_script_cython.py`.

## Verified against

- `core/pyx/` listing (35 `.pyx` incl. `_shader_parse.pyx`, `_transform_soa.pyx`), each file's first `def/cpdef` (grep `^(cpdef|def)`)
- `core/build_cython.py:44-150` (`_COMPILER`, flags, `EXTENSIONS`, directives, nthreads), `setup.py` (`from core.build_cython import build`), `main.py:78-159` (25 tracked names, splash rebuild)
- `core/components/scripting/cythonp/build.py:12-100`, `cythonp/loader.py:22-80`, `core/assets/importer/settings.py:16-18`
