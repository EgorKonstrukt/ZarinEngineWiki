# Particle Force Field

Bends nearby particle systems without touching their configs: wind zones, gravity wells, vortex funnels, drag volumes, updrafts for leaves and embers.

| Inspector group / field | Meaning |
|---|---|
| Shape: Shape | Field volume type (sphere/box style) |
| Shape: Radius / Box Size | Sphere radius or box extents |
| Shape: Start Range | Distance where falloff begins |
| Force: Force X / Y / Z | Directional force vector |
| Force: Intensity | Overall strength multiplier |
| Force: Multiply by Distance | Scale the force with distance from center |
| Drag, Gravity | Extra damping and gravity inside the volume |

Place the field entity so its volume overlaps the emitter; particles entering the volume pick up the force, leaving ones keep their velocity. Stack several fields for curl-like motion (swirl + lift + drag reads as a tornado).

Workflows: campfire smoke bend — sideways force with distance falloff so smoke rises then drifts; waterfall mist — downward gravity plus outward push; magic vortex — tangential force around a center with inward pull; leaf updraft — gentle upward force over a courtyard.

Fields compose with the system's own physics additively — halve both when combining, then tune back up.

Troubleshooting: no effect → volume misses the emitter, check the gizmo overlap; violence → intensity too high for the particle mass scale, lower and raise drag; one-directional-only motion → force vector single-axis, add components.

Related: Particle System, Wind Zone (environment vegetation wind), Water (flow feel), Buoyancy (underwater drift twin).
