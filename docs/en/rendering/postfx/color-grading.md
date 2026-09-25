# Color Grading

Adjusts tone and color balance of the finished frame. Built on the shared GraphicsEffect base: enable toggle plus effect-specific Inspector fields, executed in component order after the main scene pass.

Cost guide: single-pass color ops stay cheap; multi-tap and screen-space techniques dominate the post budget, so profile before stacking.

Related: [PostFX Stack](../postfx.md), Cameras (render scale), Profiler.
