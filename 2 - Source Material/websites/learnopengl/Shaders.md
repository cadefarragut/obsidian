Shaders always begin with a version declaration, followed by a list of input and output variables, uniforms and its main function.

When we're talking specifically about the vertex shader each input variable is also known as a vertex attribute.

GLSL has most of the default basic types we know from languages like C: int, float, double, uint and bool. GLSL also features two container types that we'll be using a lot, namely vectors and matrices. 


Vectors:
vecn, bvecn, ivecn, uvec, dvecn,

Swizzling, using one vectors components to create another.

For example:
	vec4 Pos = (0, 1, 2, 3)
	vec4 new = Pos.xxyy (0, 0, 1, 1)
can also pass vectors into vector construction calls
		vec2 temp = (0, 1)
			vec4 newtemp = (temp, 0, 1) => (0, 1, 0, 1)

Shaders have inputs and outputs, (ins and outs) that are used to pass data throughout the graphics pipeline. Vertex shader is a little different from the fragment shader because the vertex shader gets its input for the vertex data, which is supplied by the code. Because of this, we also have to specify the vertex attribute configuration via the location variable at the top of the shader.

The other exception is that the fragment shader requires a vec4 color output variable, since the fragment shader needs to generate a final output color. If you fail to specify an output color in your fragment shader, the buffer will be undefined and OpenGL usually just renders them either black or white

Uniforms are another way to pass data from our application on the CPU to the shaders on the GPU. Uniforms are however slightly different compared to vertex attributes. First of all, uniforms are global. Global, meaning that a uniform variable is unique per shader program object, and can be accessed from any shader at any stage in the shader program. Second, whatever you set the uniform value to, uniforms will keep their values until they're either reset or updated.

**if you create a uniform in any stage, for example fragment shader, this uniform will be accessible from any stage at any time ( do you not have to recreate this uniform variable in vertex shader)

We first need to find the index/location of the uniform attribute in our shader. Once we have the index/location of the uniform, we can update its values. 
![[Pasted image 20260818181337.png]]
First, we retrieve the running time in seconds via glfwGetTime(). Then we vary the color in the range of 0.0-1.0 by using the sin function and store the result in greenValue.

Then we query for the location of the ourColor uniform using glGetUniformLocation. We supply the shader program and the name of the uniform (that we want to retrieve the location from) to the query function. If glGetUniformLocation returns -1, it could not find the location. Lastly we can set the uniform value using the glUniform4f function. Note that finding the uniform location does not require you to use the shader program first, but updating a uniform does require you to first use the program (by calling glUseProgram), because it sets the uniform on the currently active shader program.

Uniforms are a useful tool for setting attributes that may change every fram, or for interchanging data between your application and your shaders.