# Blend Shapes

Morph-target deformation for faces and soft props, layered over skeletal animation.

| Inspector group | Covers |
|---|---|
| Blend Shapes | Per-target weight sliders |

Weights arrive with the mesh import and are animated from clips or scripts. A smoke test covers the blendshape path, so regressions surface in CI.

Use for facial expressions, damage states and breathing loops; heavy full-body morphs belong in the skeleton instead.

Related: Skinned Mesh Renderer, Animation Clips.
