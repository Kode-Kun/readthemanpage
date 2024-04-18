+++
author = "Lui Sant'Ana Cardoso"
title = "OpenGL org-to-md test"
date = "2019-03-11"
description = "Test article to see if emacs org-mode translates well to Markdown"
tags = [
    "markdown",
    "opengl",
    "org-mode",
    "emacs",
]
categories = [
    "themes",
    "syntax",
]
series = ["Themes Guide"]
aliases = ["migrate-from-jekyl"]
toc = true
+++

This article offers a sample of basic Markdown syntax that can be used in Hugo content files, also it shows whether basic HTML elements are decorated with CSS in a Hugo theme.
<!--more-->

# Table of Contents

1.  [shit to get done <code>[1/3]</code> <code>[33%]</code>](#orgdb33b06)
2.  [Linux Specific:](#org2331859)
    1.  [Compilation:](#orgddcb5e0)
    2.  [OpenGL version:](#org8866a41)
3.  [GLAD:](#orge4257ed)
    1.  [GLAD retrieves OpenGL functions from the current host OS, so that we don&rsquo;t have to do that manually. If we did it manually it would look something like this:](#org0879b06)
    2.  [Initialize GLAD](#orge13a807)
        1.  [GLAD manages function pointers for OpenGL, so it has to be initialized before we call any OpenGL functions:](#org88a0d32)
    3.  [Set glViewport](#orgbc4c02c)
        1.  [glViewport tells OpenGL the size of the rendering window so it knows how we want to display the data and coordinates with respect to the window.](#org1b52c14)
        2.  [Update the viewport when window gets resized by user:](#orgca596a7)
4.  [GLFW:](#orgb384c67)
    1.  [Initialize GLFW:](#org07acf33)
        1.  [We first initialize GLFW](#org947c959)
        2.  [We then set the version of OpenGL with](#orgcb5aa04)
        3.  [We then set the OpenGl profile to the core profile with](#orgfe95ed2)
    2.  [Create window object:](#orgf37258e)
        1.  [Window object holds all the windowing data (width, height, title) ignore the other parameters](#org5ffb88f)
        2.  [Tell GLFW to make the context of our window the main context on the current thread:](#org66395cc)
    3.  [Keep drawing window for as long as it is not closed](#org9dbca08)
    4.  [Get input](#orgafae0f1)
        1.  [Create a function to check the return value of glfwGetKey()](#org25eb89d)
        2.  [Then call it every iteration of the render loop (a frame):](#orgef8d282)
5.  [Rendering](#org8812f13)
    1.  [ONE FRAME = ONE ITERATION OF THE RENDER LOOP](#orge7de98c)
    2.  [OpenGL rendering pipeline (refer to pipeline.png)](#org5bde381)
        1.  [VertexData[] -> Vertex Shader -> Shape Assembly -> Geometry Shader -> Rasterization -> Fragment Shader -> Tests and Blending](#org0f3e6c1)
    3.  [Main render loop](#org5ede03d)
        1.  [Clear the screen with a color at the start of every frame to get rid of the results of the previous frame](#org5b0b166)
6.  [Shaders](#orgcc7ae7c)
    1.  [Vertex Shader](#orgf66839a)
        1.  [The vertex shader takes as input a single vertex. It mainly transforms the 3D coordinates it receives as input into Normalized Device Coordinates (NDC) and it allows us to do some basic processing of the vertex attributes](#org2854767)
        2.  [VBO](#org24fe874)
        3.  [GLSL](#org6bc769a)
    2.  [Geometry Shader](#org7f0a9fc)
    3.  [Fragment Shader](#org8c4a7db)
        1.  [The fragment shader is where all the advanced OpenGL effects occur. Its main task is to calculate the final color of a pixel. It usually contains data about the scene such as lights, shadows, colors of the lights etc and it uses that to calculate the final color of the pixel.](#org20f84ab)
7.  [Off-topic:](#orgb078db8)
    1.  [Create shortcuts to directories with:](#org069c9af)



<a id="orgdb33b06"></a>

# TODO shit to get done <code>[1/3]</code> <code>[33%]</code>

-   [-] write notes on shaders (hello triangle)
    -   [X] shader types
    -   [X] Graphics Pipeline
    -   [ ] NDC (Normalized Device Coordinates)
-   [X] write shaders (hello triangle)
-   [ ] write shaders (shadertoy default)


<a id="org2331859"></a>

# Linux Specific:


<a id="orgddcb5e0"></a>

## Compilation:

-   &ldquo;For Linux users compiling with GCC, the following command line options may help you compile the project: -lglfw3 -lGL -lX11 -lpthread -lXrandr -lXi -ldl. Not correctly linking the corresponding libraries will generate many undefined reference errors.&rdquo;


<a id="org8866a41"></a>

## OpenGL version:

    glxinfo | grep -i "version"


<a id="orge4257ed"></a>

# GLAD:


<a id="org0879b06"></a>

## GLAD retrieves OpenGL functions from the current host OS, so that we don&rsquo;t have to do that manually. If we did it manually it would look something like this:

    // define the function's prototype
    typedef void (*GL_GENBUFFERS) (GLsizei, GLuint*);
    // find the function and assign it to a function pointer
    GL_GENBUFFERS glGenBuffers  = (GL_GENBUFFERS)wglGetProcAddress("glGenBuffers");
    // function can now be called as normal
    unsigned int buffer;
    glGenBuffers(1, &buffer);


<a id="orge13a807"></a>

## Initialize GLAD


<a id="org88a0d32"></a>

### GLAD manages function pointers for OpenGL, so it has to be initialized before we call any OpenGL functions:

    if(!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)){
        std::cout << "Failed to initialize GLAD" << "\n";
        return -1;
    }


<a id="orgbc4c02c"></a>

## Set glViewport


<a id="org1b52c14"></a>

### glViewport tells OpenGL the size of the rendering window so it knows how we want to display the data and coordinates with respect to the window.

    glViewport(0, 0, width, height);

1.  glViewport&rsquo;s dimensions can be set to values smaller than GLFW&rsquo;s dimensions, which would allow us to display other elements outside of the OpenGL viewport.


<a id="orgca596a7"></a>

### Update the viewport when window gets resized by user:

    
    void framebuffer_size_callback(GLFWwindow* window, int width, int height){
        glViewport(0, 0, width, height);
    }

1.  then call framebuffer<sub>size</sub><sub>callback</sub> from main:

        glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);


<a id="orgb384c67"></a>

# GLFW:


<a id="org07acf33"></a>

## Initialize GLFW:


<a id="org947c959"></a>

### We first initialize GLFW

    glfwInit();


<a id="orgcb5aa04"></a>

### We then set the version of OpenGL with

    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);


<a id="orgfe95ed2"></a>

### We then set the OpenGl profile to the core profile with

    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);


<a id="orgf37258e"></a>

## Create window object:


<a id="org5ffb88f"></a>

### Window object holds all the windowing data (width, height, title) ignore the other parameters

    GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);


<a id="org66395cc"></a>

### Tell GLFW to make the context of our window the main context on the current thread:

    glfwMakeContextCurrent(window);


<a id="org9dbca08"></a>

## Keep drawing window for as long as it is not closed

```cpp
    while(!glfwWindowShouldClose(window)){
        glfwSwapBuffers(window);
        glfwPollEvents();
    }
    int *b = malloc(sizeof(int))
```

<a id="orgafae0f1"></a>

## Get input


<a id="org25eb89d"></a>

### Create a function to check the return value of glfwGetKey()

    void process_input(GLFWwindow* window){
        if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS){
            glfwSetWindowShouldClose(window, true);
        }
    }

1.  Note: glfwGetKey() returns GLFW<sub>RELEASE</sub> if the key is not pressed


<a id="orgef8d282"></a>

### Then call it every iteration of the render loop (a frame):

    while(!glfwWindowShouldClose(window)){
        process_input(window);
    
        glfwSwapBuffers(window);
        glfwPollEvents();
        }


<a id="org8812f13"></a>

# Rendering


<a id="orge7de98c"></a>

## ONE FRAME = ONE ITERATION OF THE RENDER LOOP


<a id="org5bde381"></a>

## OpenGL rendering pipeline (refer to pipeline.png)


<a id="org0f3e6c1"></a>

### VertexData[] -> Vertex Shader -> Shape Assembly -> Geometry Shader -> Rasterization -> Fragment Shader -> Tests and Blending

1.  We can insert our own shaders at 3 different stages: Vertex Shader, Geometry Shader, and Fragment shader. A vertex shader and a fragment shader are demanded by OpenGL to render almost anything. A Geometry Shader is optional.


<a id="org5ede03d"></a>

## Main render loop


<a id="org5b0b166"></a>

### Clear the screen with a color at the start of every frame to get rid of the results of the previous frame

    glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);

1.  There are 3 possible buffer bits that can be cleared with glClear():

    -   GL<sub>COLOR</sub><sub>BUFFER</sub><sub>BIT</sub>
    -   GL<sub>DEPTH</sub><sub>BUFFER</sub><sub>BIT</sub>
    -   GL<sub>STENCIL</sub><sub>BUFFER</sub><sub>BIT</sub>


<a id="orgcc7ae7c"></a>

# Shaders


<a id="orgf66839a"></a>

## Vertex Shader


<a id="org2854767"></a>

### The vertex shader takes as input a single vertex. It mainly transforms the 3D coordinates it receives as input into Normalized Device Coordinates (NDC) and it allows us to do some basic processing of the vertex attributes


<a id="org24fe874"></a>

### VBO

1.  Vertex Buffer Objects can store a large number of vertices in the GPU&rsquo;s memory. It is usually used as a way to send vertex data to the vertex shader

2.  Generate a VBO

        unsigned int VBO;
        glGenBuffers(1, &VBO);
    
    1.  Then bind the generated buffer to GL<sub>ARRAY</sub><sub>BUFFER</sub>
    
            glBindBuffer(GL_ARRAY_BUFFER, VBO);
    
    2.  From now on any GL<sub>ARRAY</sub><sub>BUFFER</sub> buffer calls we make will be used to configure the currently bound buffer, which is VBO

3.  Send data through the VBO

        glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    
    1.  First parameter: type of buffer we want to copy data into
    
    2.  Second parameter: size of the data in bytes that we want to pass to the buffer
    
    3.  Third parameter: actual data we want to send
    
    4.  Fourth parameter: how we want the graphics card to manage the given data
    
        -   GL<sub>STREAM</sub><sub>DRAW</sub>: the data is set only once and used by the GPU at most a few times.
        -   GL<sub>STATIC</sub><sub>DRAW</sub>: the data is set only once and used many times.
        -   GL<sub>DYNAMIC</sub><sub>DRAW</sub>: the data is changed a lot and used many times.


<a id="org6bc769a"></a>

### GLSL

1.  Very basic vertex shader:

        #version 330 core
        layout (location = 0) in vec3 aPos;
        
        void main()
        {
            gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
        }


<a id="org7f0a9fc"></a>

## Geometry Shader


<a id="org8c4a7db"></a>

## Fragment Shader


<a id="org20f84ab"></a>

### The fragment shader is where all the advanced OpenGL effects occur. Its main task is to calculate the final color of a pixel. It usually contains data about the scene such as lights, shadows, colors of the lights etc and it uses that to calculate the final color of the pixel.


<a id="orgb078db8"></a>

# Off-topic:


<a id="org069c9af"></a>

## Create shortcuts to directories with:

    sudo ln -s /path/to/folder /path/to/shortcut

