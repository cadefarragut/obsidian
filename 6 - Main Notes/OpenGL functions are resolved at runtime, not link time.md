2026-08-15 16:44


Tags: [[OpenGL]]

# OpenGL functions are resolved at runtime, not link time

OpenGL functions are not in a library you link against at compile time. They live in the graphics driver installed on the machine, and they differ per vendor and per driver version - so there is no fixed address the compiler could bake in.

What your code actually has is a set of function pointers, empty until something fills them in. That's what GLAD does, it asks the driver for each function's address at runtime and stores it, so you can call 'glDrawArrays' like a normal function instead of looking up an address by hand every time.

It also explains the crash if you call an OpenGL function before 'gladLoadGLLoader'
# References
 
 Source - [[Hello Window]]