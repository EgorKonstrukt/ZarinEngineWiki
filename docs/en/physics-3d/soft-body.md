# Soft Body

Deformable mesh simulation for cloth, jelly and squashy props.

| Inspector group / field | Meaning |
|---|---|
| Soft Body: Mass / Stiffness | Weight vs rigidity |
| Bend Mode | Bending response model |
| Pressure | Inflation for closed meshes |
| Damping / Iterations | Settle behavior and solver quality |
| Gravity Scale | Local gravity multiplier |
| Vertex Radius | Collision thickness per vertex |
| Max Velocity | Stability clamp |
| Pin Mode / Pin Fraction | Attached vertex share |
| Max Vertices | Simulation budget |
| Double Sided | Two-face collision |

Vertices, center of mass and velocities exchange with the solver through the soft shared buffer every step — heavier than rigid bodies, so budget vertex counts.

Related: Buoyancy, PhysBone, Cloth-adjacent Object Effects.
