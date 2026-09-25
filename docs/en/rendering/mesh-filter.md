# MeshFilter

Answers *what* to draw: a mesh asset plus the submesh name inside multi-mesh files.

| Field | Meaning |
|---|---|
| mesh_path | Model file: `.obj`, `.fbx`, `.stl`, `.gltf`, `.glb`, `.usdz`, `.dae`, `.blend`, `.3ds` and more via Assimp C-FFI |
| mesh_name | Submesh selector within the file |

Import brings vertices, normals, UVs, tangents, colors and skin weights as numpy arrays, cached in `Library`. Per-file `.import` settings control scale, normal/tangent generation and smoothing angle, with an OBJ fallback for material groups.

Related: MeshRenderer, SkinnedMeshRenderer, MeshCollider, Model Import page.
