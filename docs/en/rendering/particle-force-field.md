# Particle Force Field

Bends nearby particle systems: wind zones, gravity wells, vortexes, drag volumes.

| Inspector group / field | Meaning |
|---|---|
| Shape: Shape | Field volume type |
| Shape: Radius / Box Size | Sphere radius or box extents |
| Shape: Start Range | Falloff start distance |
| Force: Force X / Y / Z | Directional force vector |
| Force: Intensity | Overall strength |
| Force: Multiply by Distance | Scale force with distance |
| Drag, Gravity | Damping and gravity inside the volume |

Place the field entity so its volume overlaps the emitter. Stack several fields for curl-like motion.

Related: Particle System, Wind Zone (environment), Water (flow).
