# Soft Body

Deformable mesh simulation for cloth, jelly, squashy props and soft characters — vertices simulated, mesh follows.

| Inspector group / field | Meaning |
|---|---|
| Soft Body: Mass / Stiffness | Weight vs shape retention |
| Bend Mode | Bending response model selector |
| Pressure | Inflation for closed meshes (balloons, tires) |
| Damping / Iterations | Settle behavior and solver quality per step |
| Gravity Scale | Local gravity multiplier |
| Vertex Radius | Collision thickness per vertex |
| Max Velocity | Stability clamp |
| Pin Mode / Pin Fraction | Share of vertices attached to the anchor |
| Max Vertices | Simulation budget cap |
| Double Sided | Two-face collision for thin cloth |

Vertices, center of mass and velocities exchange with the solver through the soft shared buffer every step — heavier than rigid bodies, so budget vertex counts (hundreds, not thousands) and reserve soft bodies for hero props.

Pin patterns: full pin row for curtains and capes, corner pins for tablecloths, zero pins for free jelly. Pressure turns closed meshes into bouncy balls; combine with high damping for waterbeds.

Iterations are the quality knob: raise until squash recovers cleanly, then stop — cost grows linearly. Damping too low jiggles forever; too high feels frozen.

Workflow: closed low-poly mesh → SoftBody with pressure for balls, open mesh with pins for cloth → tune stiffness/damping → verify gizmo volume → Play and poke it with a kinematic pusher.

Troubleshooting: explodes → iterations too low for the stiffness, raise both carefully; falls through → vertex radius tiny plus fast motion, grow radius or slow down; pins tear → pin fraction too small for the load, add pins; heavy → max vertices overshot, decimate the source mesh.

Related: Buoyancy (floating jelly), PhysBone (chain alternative for straps), Cloth-adjacent Object Effects, Rigidbody (pusher bodies).
