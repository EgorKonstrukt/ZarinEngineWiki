# Joints

Connect two bodies with a constraint: doors, wheels, suspension, ragdoll limbs, breakable attachments, jelly mounts.

| Field | Default | Meaning |
|---|---|---|
| connected_entity_name | — | Name of the other entity (must carry Rigidbody + collider) |
| anchor | origin | Pivot in local space |
| axis | (0,0,1) | Hinge/spring axis |
| joint_type | hinge | `HINGE`, `FIXED` or `SPRING` |
| limit_low / limit_high | −π / +π | Angular travel, radians |
| stiffness | 10.0 | Spring stiffness |
| damping | 1.0 | Spring oscillation damping |

Type guide:

- `HINGE` — rotation about one axis within limits: doors (limits 0…π/2), wheels (free spin, limits ±π), levers.
- `FIXED` — rigid attachment that still routes through the solver: crates strapped to trucks, breakable by removing the component from a script at a force threshold.
- `SPRING` — elastic link with stiffness/damping: suspension (stiff, damped), jelly mounts (soft, bouncy), trailer hitches.

Setup rules: both bodies must exist and be registered before the joint resolves — create the pair before Play or in deterministic `on_awake` order. Anchor in local space of the joint owner; a misplaced anchor reads as a stretched, fighting constraint — visualize it via the joint gizmo (anchors and axes draw in the viewport).

Tuning: start limits wide, stiffness low; narrow and stiffen until motion looks right. Over-stiff springs with heavy masses jitter — raise damping first, then solver iterations, then reduce the mass ratio.

Troubleshooting: bodies explode apart → anchor far from both centers or limits inverted (low > high); joint ignores → connected entity name typo or missing Rigidbody; wobble at rest → stiffness/damping mismatch, raise damping.

Related: Rigidbody, Motor (driven joints), PhysBone (chain alternative), CharacterController (player boarding).
