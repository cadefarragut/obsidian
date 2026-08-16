2026-08-16 17:19


Tags: [[OpenGL]]

# Rendering Geometry with the OpenGL Graphics Pipeline

![[Pasted image 20260816122000.png|582]]
(Geometry shader is optional)

The graphics pipeline is the process of transforming vertex data into pixels on the screen. Vertex data first passes through the vertex shader, which processes each vertex and outputs its position along with any additional vertex attributes (color, etc.). The vertices are then assembled into primitives, such as triangles, during primitive assembly. These primitives are rasterized into fragments, which are processed by the fragment shader to determine their output color. Finally, fragments undergo tests such as depth testing and their results may be blended with the framebuffer before contributing to the final image.

![[Pasted image 20260816164651.png|592]]

Vertex data is stored in Vertex Buffer Objects (VBOs). A VBO contains the raw data for vertices, such as their positions and colors. However, the data in a VBO is simply a sequence of bytes, so OpenGL also needs to know how those bytes should be interpreted as vertex attributes.

glVertexAttribPointer describes the layout of the vertex attribute, including its type, size, stride, and offset. A Vertex Array Object (VAO) stores the vertex attribute configuration, allowing the configuration to be restored by binding the VAO instead of configuring the vertex attributes again.

An Element Buffer Object (EBO) stores indices that reference vertices stored in vertex buffers. This allows the same vertex to be reused when multiple primitives share it, rather than storing duplicate vertex data.

glDrawArrays draws primitives directly from the vertex data, while glDrawElements uses indices from an EBO to determine which vertices should be used.




# References
