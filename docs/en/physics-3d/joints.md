# Joints

Connect two bodies with a constraint. Fields:

| Field | Default | Meaning |
|---|---|---|
| `connected_entity_name` | — | Name of the other entity |
| `anchor` | origin | Pivot in local space |
| `axis` | (0,0,1) | Hinge/spring axis |
| `joint_type` | hinge | `HINGE`, `FIXED` or `SPRING` |
| `limit_low` | −π | Lower angular limit, radians |
| `limit_high` | +π | Upper angular limit, radians |
| `stiffness` | 10.0 | Spring stiffness |
| `damping` | 1.0 | Spring damping |

Use hinges for doors and wheels (set limits to lock an axis), fixed joints for breakable attachments, springs for suspension and jelly mounts. The connected entity must have its own `Rigidbody` + collider; both bodies must be registered before the joint resolves, so prefer creating the pair before Play or in `on_awake` order.
