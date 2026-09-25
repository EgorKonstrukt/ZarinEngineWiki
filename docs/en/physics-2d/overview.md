# Physics 2D Overview

A separate lightweight 2D pipeline with its own components, simulated by the same solver but flagged as 2D: out-of-plane velocity (`vz`, tilt rates) is zeroed every step so bodies stay in-plane.

Use it for side-scrollers, top-down games and 2D UI-plane physics where full 3D bodies would be overkill. 2D shapes map onto the solver as flat primitives: `Box2D → box` with size `[sx, sy, 1.0]`, `Circle2D → sphere`, offsets as `[ox, oy, 0]`.

Scripts use the same collision callbacks (`on_collision_enter/stay/exit`) and the same fixed step (`dt = 0.02`).
