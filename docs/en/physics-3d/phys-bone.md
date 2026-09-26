# PhysBone

Bone-chain dynamics for hair, tails, capes, antennae, dangling jewelry and monster appendages — spring simulation along an existing bone chain.

| Inspector group | Covers |
|---|---|
| Transforms | Root Transform, Ignore Transforms, Ignore Other Phys Bones, Endpoint Position, Multi Child Type (branching behavior) |
| Forces | Integration Type, Pull (+Curve), Spring (+Curve), Damping (+Curve) and further force tuning |
| Limits / Colliders | Limit Type, Immobile handling, attached PhysBoneCollider set |

Curves over chain length are the secret weapon: stiff roots with floppy tips read natural — flat values read robotic. Pull fights gravity (high pull = floaty), spring restores shape, damping kills oscillation.

Branching: Multi Child Type controls how forks (fingers, ponytail strands) distribute — test on the actual asset, fork behavior varies by hierarchy.

Colliders deflect the chain every step; see PhysBoneCollider for sphere/capsule/plane shapes. Chains ignore each other unless configured — sleeves clipping capes need explicit ignore rules or colliders.

Workflow: rigged chain bones → PhysBone on the root with endpoint set → colliders on head/torso → tune pull/spring/damping with curves → stress-test with fast head turns in Play.

Troubleshooting: stretches like rubber → pull too low or damping zero; vibrates → spring too high for the mass scale, lower and raise damping; clips through body → missing collider or ignore rule swallowing it; frozen → immobile type misassigned.

Related: PhysBoneCollider, Armature, Skinned Mesh Renderer, IK notes in Animation, Cloth Object Effects (static alternative).
