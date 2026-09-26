# VideoRenderer

Plays video clips onto a mesh surface: in-world screens, briefing walls, cutscene planes, animated billboards and fake windows.

Playback runs through the internal `Video` shader backed by the engine video decoder — the surface is a normal mesh, so lighting, shadows and post apply consistently with the scene. Position and orient with the entity Transform; pair with a Projector throwing matching light for cinema feel.

Workflows: briefing room — quad wall with the clip, dim the room lights, one spot on the audience; CCTV wall — grid of small quads each with its own clip; animated signage — looping clip instead of a shader scroll; cutscene plane — fullscreen-quad camera-facing video with letterbox bars from GUI panels.

For readable screens in dark scenes, bias the surface material toward emissive so the image survives low ambient. Mind VRAM: several HD clips decode concurrently — keep counts low or resolutions modest.

Troubleshooting: black screen → clip path broken or decoder missing the codec, re-encode to a supported container; stutter → decode-bound, lower resolution; tearing against audio → the component is video-only, sync narration via AudioSource timelines.

Related: SpriteRenderer, Materials, Projector (cinema pairing), AudioSource (companion sound).
