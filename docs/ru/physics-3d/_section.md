# Физика 3D

Симуляция твёрдых тел: тела `Rigidbody` + формы коллайдеров, шагаемые подключаемым солвером.

- [Обзор физики 3D](overview.md) — конвейер, режимы, таксономия тел.
- [Rigidbody](rigidbody.md) — масса, силы, скорости, заморозка осей.
- [Box-коллайдер](box-collider.md) — самый дешёвый примитив.
- [Sphere-коллайдер](sphere-collider.md) — шары, катание, триггеры.
- [Capsule-коллайдер](capsule-collider.md) — персонажи, пилюли.
- [Mesh-коллайдер](mesh-collider.md) — точный/convex/box/sphere из геометрии.
- [Terrain-коллайдер](terrain-collider.md) — heightfield ландшафтов.
- [GS Volume Collider](gs-volume-collider.md) — Gaussian-splat объёмы боксами.
- [Контроллер персонажа](character-controller.md) — FPS/TPS кинематическое тело.
- [Джойнты](joints.md) — связи hinge/fixed/spring.
- [Физические материалы](physics-materials.md) — трение/упругость.
- [Слои коллизий](collision-layers.md) — кто с кем сталкивается.
- [Солвер и многопоточность](solver-threading.md) — режимы, буферы, шаг.
- [Плавучесть](buoyancy.md) — тела на водных объёмах.
- [Soft Body](soft-body.md) — деформируемые меши.
- [PhysBone](phys-bone.md) — пружинная динамика костей.
- [PhysBoneCollider](phys-bone-collider.md) — отражатели PhysBone.
- [Мотор](motor.md) — постоянный hinge/slider-привод.

## Проверено по

- `core/components/physics/` (rigidbody, colliders, joint, motor, buoyancy, soft_body, physbone, pbcollider, character, gsvol, meshcol, terrain_collider)
- `core/physics/` (интерфейс солвера, сцена, shapep, воркеры, процесс)
- `physics_solvers/registry.py`
