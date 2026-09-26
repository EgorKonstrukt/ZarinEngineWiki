# Blend Shapes

Morph-target deformation for faces and soft props, layered over skeletal animation: smiles, blinks, damage dents, breathing.

| Inspector group | Covers |
|---|---|
| Blend Shapes | Per-target weight sliders, 0–100 style range |

Weights arrive with the mesh import and animate from clips or scripts (drive a smile weight from dialogue events). A smoke test covers the blendshape path, so regressions surface in CI before they reach artists.

Use for facial expressions, damage states and breathing loops; heavy full-body morphs belong in the skeleton instead — morphs interpolate vertices linearly and cannot rotate joints.

Workflow: sculpt targets in the DCC with identical vertex order → import → verify sliders move the right regions → wire key expressions to animation curves or script floats.

Troubleshooting: distorted mesh → vertex order mismatch between base and target, re-export cleanly; no motion → weights zero or clip not bound; seams splitting → target missing welded vertices, fix in DCC.

Related: Skinned Mesh Renderer, Animation Clips, Armature.
