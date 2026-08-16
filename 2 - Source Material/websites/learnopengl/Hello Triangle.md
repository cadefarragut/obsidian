In OpenGL everything is in 3D space, but the screen or window is a 2D array of pixels so a large part of OpenGL's work is about transforming all 3D coordinates to 2D pixels that fit on your screen. This process is managed by the graphics pipeline. There are 2 parts of the graphics pipeline, transforming your 3D coordinates into 2D coordinates on the  screen, and transforming these 2D coordinates into actual colored pixels. The input of the graphics pipeline is a 3D coordinate and each step of the pipeline takes the input of the previous stage. The pipeline programs to do this are called shaders, some of which are configurable by the developer which are colored in blue.

![[Pasted image 20260816122000.png]]

The input to the graphics pipeline is a list of 3D coordinates that should form a triangle called Vertex Data. A vertex is a collection of data per 3D coordinate, represented using vertex attributes that can contain any data we'd like.

In order for OpenGL to know what to make of your collection of coordinates and color values OpenGL requires you to hint what kind of render types you want to form with the data. Do we want the data rendered as a collection of points, a collection of triangles or perhaps just one long line? Those hints are called primitives and are given to OpenGL while calling any of the drawing commands. Some of these hints are GL_POINTS, GL_TRIANGLES and GL_LINE_STRIP

The first part of the pipeline is the vertex shader that takes as input a single vertex. This tranforms 3D coordinates into 3D coordinates (more on that later) and processe some of the vertex attributes. 

The output is passed to the Geometry Shader. The geometry shader takes as input a collection of vertices that form a primitive and has the ability to generate other shapes by emitting new vertices to form new (or other) primitives. In this case, it generates a second triangle out of the given shape.

The Primitive Assembly take as input all vertices(or vertex if GL_POINTS is chosen) from the vertex or gemetry shader that form one or more primitives and assembles all the points in the primitive shape given.

The output of the primitive assembly is passed to the rasterization stage where it maps the resulting primitve(s) to the corresponding pixels on the final screen, resulting in fragments for the fragment shader to use.  Before the fragment shader runs, clipping is performed. Clipping discards all fragments that outside your view, increasing performance. A Fragment in OpenGL is all the data required for OpenGL to render a single pixel.

The main purpose of the fragment shader is to calculate the final color of the pixel and this is usually the stage where all the advanced OpenGL effects occur. Usually the fragment shader contains ata about the 3D scene that it can sue to calculate the final pixel color (like lights, shadows, color of the light and so on)

The final stage is tests/blending, this takes in the fragment and checks for its corresponding depth to see if the fragment is in front or behind other objects to be discarded. It also checks for alpha values (which defines opacity) and blends objects accordingly. So the fragment shader can calculate one thing, and the final output could be completely different based on this stage.

OpenGL requires you to at least define a vertex and fragment shader.

OpenGL only process 3D coordinates when they're in a specific range between -1.0 and 1.0 on all 3 axes. These are called normalized device coordinates (NDC). Your NDC coordinates will be transformed to screen-space coordinates via the viewport transform using the data you provided with glViewport. The resulting screen-space coordinates are then transformed to fragments as inputs to your fragment shader.

To send vertex data to the first process of the graphics pipeline, we need to create memory on the GPU where we store the vertex data, configure how OpenGL should interpret the memory and specify how to send the data to the GPU. We manage this memory via Vertex Buffer Objects (VBO) that can store large number of vertices in the GPU's memory. The VBO helps us be able to send a group of vertexes to the GPU memory instead of sending them one by one.

We need to generate the buffer and store it with an ID so we know how to access it. The buffer type of a vertex buffer object is GL_ARRAY_BUFFER. OpenGL allows us to bind to several buffers at once as log as they have a different buffer type. We can bind the newly created buffer to the GL_ARRAY_BUFFER target with the glBindBuffer function. From that point on any calls we make (on the GL_ARRAY_BUFFER target) will be used to configure the currently bound buffer, which is VBO. Then we can make a call to the glBufferData function that copies the previously defined vertex data into the buffer's memory. glBufferData is a function specifically targeted to copy user-degined data into the currently bound  buffer. The first argument is the type of buffer we want to copy data into, the VBO is cound to GL_ARRAY_BUFFER. The second argument specifies the size of the data, the third is the data itself. The fourth specifies how we want the graphics card to manage the given data. This can take 3 forms:
	- GL_STREAM_DRAW: the data is set only once and used by the GPU at most a few times
	- GL_STATIC_DRAW: the data is set only once and used many times
	- GL_DYNAMIC_DRAW: the data is changes a lot and used many times
	![[Pasted image 20260816125645.png]]
	![[Pasted image 20260816125652.png]]
	![[Pasted image 20260816125700.png]]


The vertex shader is written in GLSL (GL Shader Language), it is similar to C. The built in output of the vertex shader is gl_Position. In order for OpenGL to use the shader we have to dynamically compile it at run time from its source code.

first we create the shader object, create the shader (vertex shader), attach the shader source, and compile
![[Pasted image 20260816131200.png]]

![[Pasted image 20260816131212.png]]

![[Pasted image 20260816131222.png]]

The same process is done for the Fragment Shader, the fragment shader outputs an RGBA fragment color/opacity. This variable you have to define yourself and is not baked in like gl_Position.

A shader program is the final linked version of multiple shaders combined. To use shaders we have to link them to a shader program object and then activate this shader program.

After creating our shaders, our shader program, and linking them together. We have now given the GPU the information of our vertex. However, we need to tell the GPU how to interpret these values. We have to manually specify what part of our input data goes to which vertex attribute in the vertex shader. 

Our Vertex Buffer data is formatted as follows:
![[Pasted image 20260816133258.png]]

The position data is stored as 32 bits (4byte) floating point values. Each position is composed of 3 of those values. There is no space (or other values) between each set of 3 values. The values are tightly packed in the array. The first value in the data is at the beginning of the buffer.

We tell OpenGL how to interpret this vertex data (per vertex attribute) using glVertexAttribPointer
![[Pasted image 20260816133508.png]]

First parameter specifies which vertex attribute we want to conigure. Remember that we specified the location of the position vertex attribute in the vertex shader with layout (location = 0). This sets the location of the vertex attribute to 0 and since we want to pass data to this vertex attribute, we pass in 0.

Next argument specifies the size of the vertex attribute. The vertex attribute is a vec3 so it is composed of 3 values.

third is the type of value which is float

The next argument specifies if we want the data to be normalized. This is not relevant to use so we put FALSE

fifth is known as stride and tells us the pace between consecutive vertex attributes. Since the next set of position data is located exactly 3 times the size of float away we specify that value as the stride. If the vertex attributes are tightly packed, we can use 0 and OpenGL will calculate the stride for us.

The last is of ype void* and thus required a weird case. This is the offset of where the position data begins in the buffer. Since its at the start, we just put 0.

When you call the glVertexAtribPointer, the attribute data that is used is from the VBO that is currently bounded. We can have multiple VBOs and Bind them at different times.

Vertex Array Object (VAO) can be bound just like a vertex buffer objec and any subsequent vertex attribute calls from that point on will be stored inside teh VAO. This has the advantage that when configuring vertex attribute pointers you only have to make those calls once and whenever we want to draw the object, we can just bind the corresponding VAO. This makes switching between different vertex data and attribute configurations as easy as binding a differnet VAO. All the state we just set is stored inside the VAO. All the state we just set is stored inside the VAO.

A VAO stores the following:
- Calls to glEnableVertexAttribArray or glDisableVertexAttribArray
- Vertex attribute configurations via glVertexAttribPointer
- Vertex buffer objects associated with vertex attributes by calls to glVertexAttribPointer

![[Pasted image 20260816135318.png]]

To draw our objects of choice, OpenGL provides us with the glDrawArrays function that draws primitives using the currently active shader, the previously defined vertex attribute configuration and with the VBO's vertex data (Indirectly bound via the VAO)


Let's say we want to draw a rectangle with two triangles, 2 of the 6 vertices would be repeated. This might not be an issue for a single rectangle, but if you are processing 1000s of rectangles, this will be an issue.  We want to only store the unique vertices and then specify the order at which we want to draw them. This is what EBOs are for.

If we were to just use a VBO to create a rectangle, this is what it would look like:
![[Pasted image 20260816163925.png]]

6 separate vertexes, some repeating.

Element Buffer Objects (EBO) is a buffer, just like a vertex buffer object (VBO), that stores indices that OpenGL uses to device what vertices to draw. This so called indexed drawing is exactly the solution to our problem.  

Now if we wanted to create the image, we could pass in the 4 unique vertexes, and then inside the EBO pass the 6 indices of the rectangle.
![[Pasted image 20260816164059.png]]

EBO is the same as VBO in how you create it, you have to bind it before passing the buffer data. The Buffer type now though it GL_ELEMENT_ARRAY_BUFFER, and now we have to run glDrawElements, instead of glDrawArrays.

glDrawElements function takes its indices from the EBO currently bound to the GL_ELEMENT_ARRAY_BUFFER target. This means we have to bind the corresponding EBO each time we want to render an object with indices again which again is a bit cumbersome. It just so happens that a vertex array object also keeps track of element buffer object bindings. The last element buffer object that gets bound while a VAO is bound, is stored as the VAO's element buffer object. Binding to a VAO then also automatically binds that EBO

![[Pasted image 20260816164651.png]]

VAO stores the glBindBuffer calls when the target is GL_ELEMENT_ARRAY_BUFFER. This also means it stores its unbind calls so make sure you don't unbind the element array buffer before unbinding your VAO, otherwise it does not have an EBO configured.

