# MeshFilter

Answers *what* to draw: a mesh asset plus the submesh name inside multi-mesh files. No appearance settings — those live on the renderer.

| Field | Meaning |
|---|---|
| mesh_path | Model file: `.obj`, `.fbx`, `.stl`, `.gltf`, `.glb`, `.usdz`, `.dae`, `.blend`, `.3ds` and more via the Assimp C-FFI bridge |
| mesh_name | Submesh selector within the file |

Import pipeline: Assimp reads vertices, normals, UVs, tangents, colors and skin weights into numpy arrays (OBJ parser as fallback for material groups, GIF flipbook importer for animated sprites-as-frames), the result caches in `Library`, and the geometry cache uploads once to the GPU. Per-file `.import` JSON controls scale, normal/tangent generation and smoothing angle.

One mesh asset feeds three consumers at once: MeshRenderer (visuals), MeshCollider (physics) and the model preview thumbnail — a single reimport updates all of them.

Workflow: import the model, check it in the model preview, set `.import` scale so one unit equals one meter, assign into MeshFilter, add materials per submesh on the renderer. Multi-part FBX: one entity per `mesh_name` sharing the same path.

Troubleshooting: missing submesh → `mesh_name` typo or stale import, reimport and re-pick; wrong scale → `.import` scale vs scene units; black normals → enable normal generation in `.import`; pink → renderer material path broken, not the filter.

Related: MeshRenderer, SkinnedMeshRenderer, MeshCollider, Model Import, Material Preview.
