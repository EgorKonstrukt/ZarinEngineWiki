# PhysBoneCollider

Deflectors that keep PhysBone chains out of bodies: hair out of heads, capes out of legs, tails out of walls.

| Inspector group / field | Meaning |
|---|---|
| Shape: Shape Type | Sphere, capsule or plane volume |
| Radius / Height / Center | Shape dimensions in local space |
| Direction / Plane Normal | Orientation of capsules and planes |
| Behavior: Inside Bounds | How deep penetration resolves (push out vs slide) |
| Gizmos: Show Gizmo | Volume visualization in the viewport |

Attach to the body part the chain must avoid and size slightly larger than the visual mesh — chains simulate on bones, which sit inside the mesh, so skin-tight colliders still clip. The chain slides around the volume at runtime instead of passing through.

Patterns: head sphere + torso capsule for long hair; thigh capsules for coats; plane for floors under crawling creatures; inside-bounds mode for earrings that must stay near the ear.

Keep collider counts per chain low (1–3): every collider tests every chain particle every step.

Troubleshooting: still clips → collider smaller than the bone offset, grow it; chain pushed away entirely → collider engulfs the root, move it down the chain; jitter on contact → inside-bounds mode fighting the limit, switch behavior.

Related: PhysBone, Armature, Sphere/Capsule Collider (static-world equivalents).
