2026-08-15 17:16


Tags: [[OpenGL]]

# OpenGL specifies rendering only, so windowing and loading are external

OpenGL is a specification for drawing, and only drawing. It says nothing about where the pixels go, how to get a window, or how to reach the driver's functions. Every one of those gaps has to be filled by something outside the spec, which is why several separate dependencies show up before you can draw anything.

- **The graphics driver** implements OpenGL. It ships with your GPU, not with your project, which is why there's no "OpenGL library" to install.
- **GLFW** creates the window, the context, and handles input, the OS-facing half OpenGL deliberately does not touch.
- **GLAD** resolves function addressing at runtime. See [[OpenGL functions are resolved at runtime, not link time]]

GLM is usually added too, but for a different reason, modern OpenGL removed the built-in matrix stack, so transformation math is now the application's job. It's convenience, but it is not required.

The specific libraries are swappable, SDL instead of GLFW, GLEW instead of GLAD. What's fixed is the set of gaps.



# References
Source - [[Hello Window]]