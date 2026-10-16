# Cython-расширения

> 35 `.pyx`-исходников в `core/pyx/`; 33 компилируются через `core/build_cython.py:96-130` (`EXTENSIONS`, `setup.py build_ext --inplace` → `build()`). Функции входа ниже — первые `def/cpdef` каждого файла; однострочное назначение следует из них.

[Home](../../index.md)

## Сборка

```bash
python setup.py build_ext --inplace [--compiler=msvc]
```

`core/build_cython.py:18-63`: дефолт Windows — автозагружаемый компактный MinGW-w64 (`tools/mingwpm`), оверрайд `--compiler=msvc` / `ZARIN_COMPILER=msvc`; директивы `boundscheck=False, wraparound=False, cdivision=True, infer_types=True`, `nthreads=os.cpu_count() or 4` (`build_cython.py:132-150`). Флаги: MinGW `-O2 -DNDEBUG -march=x86-64 -mtune=generic`, MSVC `/O2 /fp:fast /arch:AVX2 /GL /DNDEBUG`, Linux `-O3 -ffast-math -march=native -DNDEBUG -flto -fopenmp` (`build_cython.py:65-83`). `main.py:78-108` отслеживает 25 имён модулей и пересобирает через сплэш, когда `.pyd` отсутствует или его `.pyx` новее (`total_exts = 25`, `main.py:126`). Не компилируются в `EXTENSIONS` (есть в `core/pyx/`, но нет `_ext(...)` в `build_cython.py:96-130`): `_shader_parse.pyx`, `_transform_soa.pyx`.

## Каталог модулей

| `.pyx` → `core.*` | одна строка (входная функция) |
|---|---|
| `_convex_hull.pyx` → `core._convex_hull` | Симплексы выпуклой оболочки по облаку точек (`convex_hull_simplices`) |
| `_bvh_build.pyx` → `core._bvh_build` | SAH-поиск лучшего сплита BVH (`sah_compute_best_split`) |
| `_raytracing_data.pyx` → `core._raytracing_data` | Построение данных ускорения рейтрейсинга |
| `math_helpers.pyx` → `core.math_helpers` | Скалярные mat4/quat/vec3 + рей-пересечения (`mat4_mul_fast`, `quat_slerp`, `ray_triangle_intersect`…) |
| `_math_vec.pyx` → `core._math_vec` | Векторные батч-ядра математики |
| `_culling.pyx` → `core._culling` | CPU-куллинг фрустума, параллельный куллинг, извлечение плоскостей, куллинг груп-инстансов (`cpu_frustum_cull…`, `cull_group_instances`) |
| `_transform_batch.pyx` → `core._transform_batch` | Батч-апдейты трансформов |
| `_render_utils.pyx` → `core._render_utils` | Рендерные утилитные ядра |
| `_physics_utils.pyx` → `core._physics_utils` | Физические утилитные ядра |
| `_core_batch.pyx` → `core._core_batch` | Ядра на flat-массивах mat4/vec3 (`mat4_mul_flat`, `rotate_around_axis_flat`, `look_at_flat`…) |
| `_types.pyx` → `core._types` | Общие C-уровневые определения типов |
| `_ecs_batch.pyx` → `core._ecs_batch` | ECS/SOA батч-апдейты, резолв parent-индексов, write-back мировых матриц (`batch_update_from_transforms`, `write_world_matrices`…) |
| `_render_batch.pyx` → `core._render_batch` | Рендерные батч-ядра |
| `_octree_batch.pyx` → `core._octree_batch` | Батч-тесты AABB: intersect/contain/ray/point/sphere (`aabb_*_batch`) |
| `_constraint_batch.pyx` → `core._constraint_batch` | Батч-солверы констрейнтов: weighted pos/scale/euler, follow, rotate-towards, scale-to (`batch_*`) |
| `_curve_batch.pyx` → `core._curve_batch` | Батч/одиночная/мульти-оценка кривых (`evaluate_curve_batch`, `evaluate_curve_single`, `evaluate_multi_curve_batch`) |
| `_constraint_update.pyx` → `core._constraint_update` | Покадровый драйвер апдейта констрейнтов (`batch_update_constraints`) |
| `_physics_sync.pyx` → `core._physics_sync` | Ядра синхронизации физика↔ECS |
| `_mesh_import.pyx` → `core._mesh_import` | Ядра импорта мешей (парс OBJ-чанков, сглаживание нормалей через `_cy_parse_obj_chunk`, `_cy_smooth_normals` в `importer/settings.py:16-18,107-109`) |
| `_skinning.pyx` → `core._skinning` | Ядра скиннинга |
| `_ik.pyx` → `core._ik` | IK-солверы: `two_bone_flat`, `fabrik_flat`, `batch_update_ik` (см. `tests/smoke/test_ik_numeric.py`) |
| `_physbone.pyx` → `core._physbone` | Степпер PhysBone (`step_physbone`) |
| `_audio_dsp.pyx` → `core._audio_dsp_cy` | DSP-ядра ring-буфера: `push_samples`, `mix_add`, `extract_last`, `analyze_spectrum` |
| `_raycast.pyx` → `core._raycast` | Ядра рейкаста |
| `_shadow_batch.pyx` → `core._shadow_batch` | Теневые батч-ядра |
| `_scene_query.pyx` → `core._scene_query` | Ядра запросов сцены (под `Scene.update/fixed_update/start` через `_query` в `scene.py:355-359`) |
| `_render_collect.pyx` → `core._render_collect` | Ядра сбора рендера |
| `_math_mat4.pyx` → `core._math_mat4` | Батчевые float32 mat4-операции: identity, mul, transpose, perspective, inverse (`mat4_batch_*`) |
| `_vr_batch.pyx` → `core._vr_batch` | VR батч-ядра |
| `_nav_batch.pyx` → `core._nav_batch` | Навигационные батч-ядра |
| `_splat_sort.pyx` → `core._splat_sort` | Ядра сортировки гауссовых сплатов |
| `_gs_volume.pyx` → `core._gs_volume` | Ядра occupancy GS-вольюмов: `splat_occupancy`, `dilate26`, `greedy_merge_boxes` |
| `_sky_astro.pyx` → `core._sky_astro` | Небесно-астрономические ядра |

## Юзерский Cython (компаньоны скриптов)

Отдельно от расширений движка: `core/components/scripting/cythonp/` собирает `.pyx` рядом с юзерским `.py` при первом импорте, пересобирает при изменении (hot-reload совместим). Раскладка: `CACHE_SUBDIR = "cache/cython"` (`cythonp/build.py:12`); per-script директория `build/<sha256(script_dir)[:16]>` (`build.py:48-55`); per-version директория = фингерпринт исходника `sha256(scheme + cython_version + compiler + pyx [+ pxd])[:12]` (`build.py:56-80`); `_KEEP_VERSIONS = 3` чистится через `_prune_versions` (`build.py:14,93-100`); шаблон `CYTHON_TEMPLATE` (`cythonp/loader.py:22-54`, пример `add` + `moving_average`). Покрыто `tests/smoke/test_script_cython.py`.

## Проверено по

- Листинг `core/pyx/` (35 `.pyx` вкл. `_shader_parse.pyx`, `_transform_soa.pyx`), первый `def/cpdef` каждого файла (grep `^(cpdef|def)`)
- `core/build_cython.py:44-150` (`_COMPILER`, флаги, `EXTENSIONS`, директивы, nthreads), `setup.py` (`from core.build_cython import build`), `main.py:78-159` (25 отслеживаемых имён, сплэш-пересборка)
- `core/components/scripting/cythonp/build.py:12-100`, `cythonp/loader.py:22-80`, `core/assets/importer/settings.py:16-18`
