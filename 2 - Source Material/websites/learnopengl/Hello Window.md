

GLFW is the library used to create a window for OpenGL. The reason it is used is because each Operating system has its specific operation methods to do this, so OpenGL decides not to bother. For this reason, we have to use a separate library to do it for us.

**OpenGL does not have an operations for creating a window and allowing features such as inputs (Mouse, Keyboards, controllers, time and window events, etc) and outputs (Windows, Monitors, etc). It supports OpenGL and Vulkan and it used on multiple OS **


CMake is a tool that can generate project/solution files o the user's choice (e.g. Visual Studio, Code::Blocks, Exlipse) from a collection of course code files useing pre-defined CMake scripts. This allows us to generate a Visual Studio 2019 project file from GLFW's source package which we cna use to compile the library. CMake is used because providing source code to the open world does not always work since people use different IDE or build systems for developing their application, which means the project/solution files provided may not be compatible wiht other people's setup.

**Open source code can be dependent on developer environments (IDEs, packages, build systems, etc). To fix this issue, CMake can be used to generate and build project files, using CMake allows projects to be transfered and used on differnet environments and still build properly.

OpenGL is only really a standard/specification it is up to the driver manufacturer to implement the specification to a driver that the specific graphics card supports. Since there are many different versions of OpenGL drivers, the location of most of its functions is not know at compile-time and needs to be queried at run-time. It is then the task of the developer to retrieve the location of the function he/she needs and store them in function pointers for later use. Retrieving those locations is OS-specific. In Windows, it looks something like this:

![[Pasted image 20260813235557.png]]

As you can see the code looks complex and it's a cumbersome process to do this for each function you may need that is not yet declared. Thankfully, there are libraries for this purpose as well where GLAD is a popular and up to date library.

GLAD is an open source library that manages all that cumbersome work we talked about. GLAD has a slightly different configuration setup than most common open source libraries. GLAD uses a web service where we can tell GLAD for which version of OpenGL we'd like to define and load relevant OpenGL functions according to that version.

**OpenGL is a graphics API that is reliant on specific drivers to work correctly. These drivers change based on the GPU that is being used. Because of this, when compiling and running a project, the user has to specify which version of the function that is being used, and then store it in a function pointer for later use. To avoid this annoyance and complexity, we use GLAD, which is an open source library that manages these configurations.


we will instantiate the GLFW window, In the main function we first  initialize GLFW with glfwInit, after which we can configure GLFW using glfwWindowHint. The first argument of glfwWindowHint tells us what option we want to configure, where we can select the option from the large enum of possible options prefixed with GLFW_. The second argument is an integer that sets the value of our option. Since the focus of this book is on OpenGL version 3.3 we'd like to tell GLFW that 3.3 is the OpenGL version we want to use. This way GLFW can make the proper arrangements when creating the OpenGL context. This ensures that when a user does not have the proper OpenGL version GLFW fials to run. We set the major and minor version both to 3/ We also tell GLFW we want to explicitly use the core-profile. Telling GLFW we want to use the core-profile means we'll get access to a smaller subset of OpenGL features without backwards-compatible features we no longer need. Note that on Mac OS X you need to add glfwWindowHint(GLFW_OPENGL_FORWARED_COMPAT, GL_TRUE); to initialize code for it to work.
![[Pasted image 20260815143636.png]]

**To create the window first we must initialize glfw, afterwards we have to configure glfw to the configurations of our project. One being the OpenGL versio, which is 3.3. Afterwards we set the OpenGL profile, which we use GLFW_OPENGL_CORE_PROFILE. The Profile lets glfw know whicb set of OpenGL functions/features we will be using. In this case we are just using the basic core OpenGL features and nothing extra. For Mac OS, you must use forward compatibility because Mac OpenGL drivers are not the same as window/linux.

The window object holds all the windowing data and is required by most of GLFW's other functions
The glfwCreateWindow function requires the window width and height as its first two arguments respectively. The third argument allows us to create a name for the window. We can ignore the last 2 parameters. The function returns a GLFWwindow object that we'll later need for other GLFW operations. After that we tell GLFW to make the context of our window the main context on the current thread.
![[Pasted image 20260815152503.png]]

**Creating a window object we will have to use hte glfwCreateWindow() function with parameters such as width, height and name. We then need to make the window the current context which we are rendering everything inside.

we want to initialize GLAD before we call any OpenGL function.
We pass GLAD the function to load the address of the OpenGL function pointers which is OS-specific. GLFW gives us glfwGetProcAddress that defines the correct function based on which OS we're compiling for.
![[Pasted image 20260815153105.png]]
**We need to now initialize glad, we have to pass in the function that allows glad to point to. GLFW has a built in function to find this function we are passing that is OS specific.

We have to tell OpenGL the size of the rendering window so OpenGL knows how we want to display the data and coordinates with respect to the window. We can set those dimensions via the glViewport function. The first two parameters of glViewport set the location of the lower left corner of the window. The third and fourth parameter set the width and height of the rendering window in pixels, which we set equal to GLFW's window size. We could actually set the viewport dimensions at values smaller than GLFW's dimensions; then all the OpenGL rendering would be displayed in a smaller window and we could for example display other elements outside the OpenGL Viewport.

Behind the scenes OpenGL uses the data specified via glViewport to transform the 2D corrdinates it processed to corrdinates on your screen. For example, a processed point of location (-0.5, 0.5) would be mapped to (200, 450) in screen coordinates. Note that processed corrdinates in OpenGL are between -1 and 1 so we effectively map from the range (-1 to 1) and (0, wdith) and (0, height)

**First we have to use the function glViewport to let OpenGL know the size and coordinate mapping of our window context so it knows where to locate what we are changing in the code. Processed coordinates in OpenGL are mapped between -1 and 1 instead of the pixel numbers of your screen (Keep that in mind)

There are many callback functions we can et to register our own functions. For example, we can make a callback function to process joystick input changes, process error messages etc. We register the callback functions after we've created the window and before the render loop is initiated.

**The function glfwSetFramebufferSizeCallback() is used to adjust the viewport dynamically if the window size changes. There are other call back functions can create to register dynamic inputs, liek changing the window size, controller inputs, etc

We don't want the application to draw a single image and then immediately quit and close the window. We want the application to keep drawing images and handling user input until the program has been explicitly told to stop. For this reason we have to create a while loop, that we now call the render loop, that keeps on running until we tell GLFW to stop. The glfwWindowShouldCLose function checks at the start of each loop iteration if GLFW has been instructed to close. If so, the functions returns true and the render loop stops running, after which we can close the application. The glfwPollevents function checks if any events are triggered ( like keyboard input or mouse movement events), updates the window state, and calls the corresponding functions (which we can register via callback methods). The glfwSwapBuffers will swap the color buffer (a large 2D buffer than contains color values for each pixel in GLFW's window) that is used to render during this render iteration adn show it as output to the screen.

![[Pasted image 20260815160604.png]]

**We create a render loop which constantly runs to draw images on to the screen until explicitly told to stop. glfwWindowShouldClose() function checks if the window has been closed. the glfwPollEvents checks if any events are triggers like keyboard/mouse movements and other callback functions. glfwSwapBuffers() swaps the color buffer that is used to render durng this iteration.

When an application draws in a single buffer the resulting image may display flickering issues. This is because the resulting output image is not drawn in an instant, but drawn pixel by pixel and ususally from left to right and top to bottom. Because this image is not displayed at an instant to the user while still being rendered to, the result may contain artifacts. To circumvent these issues. windowing applications apply a double buffer for rendering. The front buffer contains the final output image that is shown at the screen, while all the rednering commands draw to the back buffer. As soon as all the rendering commands are finished we swap the back buffer to the front buffer so the image can be displayed without still being rendered to, removing all the aforementioned artifacts.

**A double buffer is used to display images to avoid flickering behaviors. the front buffer shows the image, while the back buffer is being rendered to. Once the back buffer is finished rendering, the buffers swap so the back buffer is now being displayed with the new image. The process continues.

At the start of the frame we want to clear the screen. Otherwise we would still see the results from the previous frame. We can clear the screen's color buffer using glClear where we pass in buffer bits to specify which buffer we would like to clear. The possible bits we can set are GL_COLOR_BUFFER_BIT, GL_DEPTH_BUFFER_BIT and GL_STENCIL_BUFFER_BUT. Right now we onl care about the color values so we only clear the color buffer. Note that we also specify the color to clear the screen with using glClearColor. Whenever we call glClear and clear the color buffer, the entire color buffer will be filled with the color as configured by glClearColor. This will result in a dark green-blueish color. As ![[Pasted image 20260815162501.png]]
