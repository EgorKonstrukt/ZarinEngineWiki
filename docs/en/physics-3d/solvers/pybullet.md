# PyBullet Solver

Bullet-based backend with per-body collision filtering, compound bodies, and spring-joint ERP mapping.

## Properties

| Field | Code default (`solver.py:16-35`) | Project-config default (`config.py:359-362`) | Notes |
|---|---|---|---|
| `fixed_time_step` | `1/60` | `0.02` | Clamped `max(0.001, …)`; per-step `internal_dt = dt / sub_steps` |
| `num_sub_steps` | `1` | `2` | Loop in `step_simulation`: `numSubSteps=1` + N `stepSimulation` calls |
| `solver_iterations` | `50` | `10` | Forwarded as `numSolverIterations` |
| `erp` | `0.4` | `0.2` | Forwarded as `erp` |
| `contact_erp` | `0.4` | `0.2` | Forwarded as `defaultContactERP` |
| `friction_erp` | `0.0` | `0.0` | Forwarded as `frictionERP` |
| `contact_breaking_threshold` | `0.02` | `0.02` | Same in both |
| `linear_damping` / `angular_damping` | `0.04` / `0.04` | `0.04` / `0.04` | Stored defaults |
| `enable_sleeping` / `headless` | `True` / `True` | — | `p.DIRECT` when headless else `p.GUI` |

## Usage in Editor

Set physics `solver` to `pybullet`. Collision layers work via `setCollisionFilterPair`: a pair collides only if `(groupA & maskB) != 0 and (groupB & maskA) != 0` (`solver.py:281-294`).

## Usage in Code

```python
from physics_solvers.pybullet_solver.solverp.solver import PyBulletSolver

solver = PyBulletSolver()
solver.initialize({
    "headless": True,
    "gravity_y": -9.81,
    "fixed_time_step": 0.02,
    "num_sub_steps": 2,
    "solver_iterations": 10,
    "erp": 0.2,
    "contact_erp": 0.2,
    "enable_sleeping": True,
})
solver.step_simulation(0.02)

# Spring joints map stiffness to constraint ERP:
# changeConstraint(erp=min(1.0, stiffness * 0.01), maxForce=100)
jid = solver.create_joint("spring", body_a, body_b, anchor=(0, 1, 0))
```

Compound bodies: `create_compound_rigid_body` splits mass by part volume (`shapep.part_volume`) and links extras with `JOINT_FIXED`; mixed trigger/solid parts log a warning and share one body.

## Fixed update notes

- `step_simulation(dt)` sets `fixedTimeStep = dt / sub_steps`, then steps N times (`solver.py:134-143`).
- `add_plane` builds a static plane plus a `100 x 0.1 x 100` box visual.

## Related

Culverin Solver, PhysX Solver, Solver & Threading, Joints, Collision Layers.

## Verified against

- `physics_solvers/pybullet_solver/solverp/solver.py:16-95` (defaults, `initialize`, parameter map)
- `physics_solvers/pybullet_solver/solverp/solver.py:134-143` (`step_simulation` sub-step loop)
- `physics_solvers/pybullet_solver/solverp/solver.py:281-294` (collision filtering)
- `config/config.py:358-368` (config defaults `2/10/0.2`)
