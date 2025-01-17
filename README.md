# Game Engine Development From Scratch
<!-- [Link to the engine's repository](https://github.com/shreejitmurthy/AsuraEngine) -->

## About
This one singular page serves as the sprawling thoughts of Shree (me) as I attempt to develop a game engine from scratch.

## Post 1 - Introduction
### 6 January 2024
So, why? Why am I making a game engine? It’s a daunting task, no doubt—very few get past the initial stages of engine development. So, why am I doing this? Well, it’s simple: I love the idea of it. The phrase "*from scratch*" just sounds awesome, and the thought of building something from the ground up and watching it grow is something I truly yearn for.

I also see this as a fantastic learning opportunity. Most of my projects have been around ~5k lines of code, so managing a larger project like a game engine will give me valuable skills that (I believe) are useful in the industry. Plus, I just love games and game development. That said, I’m a terrible game developer and can never seem to finish a game. I’ve made games from scratch before, and I’m currently working on one in OpenGL and C++. I think this project will also prepare me well for my upcoming university degree, which starts soon.

Since finishing 12th grade in 2024, I’ve had a lot of free time—or at least I did, since much of it went into relaxing after a grueling year of studies. Now, I’m channeling that time into this project.

At the end of development (if there ever *is* an end), I hope to have a functioning engine capable of creating both 2D and 3D games. Honestly, any 3D engine can make a 2D game, since graphics APIs work in 3D by default, but I’d love for my engine to handle both seamlessly.

This series of blog posts is primarily to keep myself motivated, but it might also help me learn from my mistakes—and who knows? Maybe it’ll serve as a little showcase for future employers. My programming knowledge is entirely self-taught, so I’m not entirely sure about best industry practices yet. However, with my computing degree starting soon, I’m confident my code will gradually become more professional and aligned with industry standards over time.

All external references (i.e., libraries) will be provided as hyperlinks, linking directly to their main pages.

## Post 2 - Initial Setup
### 6 January 2025
Before diving into any real programming, I need to decide on the coding environment, build system, and libraries. Initially, I was drawn to Rust and [wgpu](https://wgpu.rs/) as the “future” of graphics. I quickly whipped up a basic open-window script and started tinkering with the wgpu API, but it didn’t take long to realize this was too complex for me. Maybe it’s because Rust has an overly intricate syntax (it does), or maybe it’s just that I don’t really understand the language yet (I don’t).

At the moment, Rust doesn’t offer me anything that C++ doesn’t already provide. Perhaps as I gain more experience with C and C++, I’ll come to appreciate why so many developers love Rust. But for now, I’ll stick with C++ and [OpenGL](https://www.opengl.org/), as they’re what I’m most familiar with. I did consider [Vulkan](https://www.vulkan.org/), but writing over 1.2k lines of code just to render a triangle? No thanks.

I’ll be using [CMake](https://cmake.org/) as my build system and [SDL](https://www.libsdl.org/) for windowing and input. SDL is widely regarded as an industry standard, alongside [GLFW](https://www.glfw.org/), but I’ve worked more with SDL, so that’s my preference. I’ll be using SDL3, which, while not fully released yet, offers several quality-of-life improvements over SDL2.

As for the engine’s name, I’ve chosen “Asura” (ˈʌsʊrə).
In Hinduism and Buddhism, Asuras are a class of beings often considered demons—but let’s ignore that part. Honestly, I picked the name because it just sounds cool.

I'll start by creating a CMake build script template, on which we can add more compiled files and dependencies.
```cmake
cmake_minimum_required(VERSION 3.28)
project(Asura VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic")

set(ASURA_LIBS
)

set(ASURA_SRC
    src/main.cpp
)

add_executable(Asura ${ASURA_SRC})

target_link_libraries(Asura ${ASURA_LIBS})
```

Next I'll create the `main.cpp` file we link to in the build script.
```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

When build and ran, we get:
```
Hello, World!
```

So at least I can run C/C++ on my machine, I would have been seriously concerned if this wasn't the case.

I'll be using C++20, but it seems to me that the C++ version doesn't *really* matter, so C++17 will work all the same. Many of my personal programming conventions don't use modern and new features. 

In the next post, I'll bring in the engine's dependencies, and aim to create a simple window and clear the screen.

####  Resources:
- [CMake](https://cmake.org/)

## Post 3 - Testing the Dependencies
### 7 January 2025
Forgot to mention I'll also be using [spdlog](https://github.com/gabime/spdlog) for logging.

Today, I'll get a basic window rendering, very simply, with this code here. I won't explain the code much, but here it is:
```cpp
#include <iostream>

#include <SDL3/SDL.h>
#include <SDL3/SDL_main.h>

#include <glad/glad.h>

#include <spdlog/spdlog.h>

int main() {
    if (!SDL_Init(SDL_INIT_VIDEO)) {
        spdlog::error("ASURA::Initialise Error: {}", SDL_GetError());
    }

    SDL_GL_SetAttribute(SDL_GL_CONTEXT_MAJOR_VERSION, 4);
    SDL_GL_SetAttribute(SDL_GL_CONTEXT_MINOR_VERSION, 1);
    SDL_GL_SetAttribute(SDL_GL_CONTEXT_PROFILE_MASK, SDL_GL_CONTEXT_PROFILE_CORE);

    SDL_Window* window = SDL_CreateWindow("Window", 800, 600, SDL_WINDOW_OPENGL);
    SDL_GLContext ctx = SDL_GL_CreateContext(window);
    bool open = true;

    if (gladLoadGLLoader((GLADloadproc)SDL_GL_GetProcAddress) == 0) {
        spdlog::error("ASURA::Failed to initialise GLAD");
    }

    SDL_GL_SetSwapInterval(1);

    SDL_Event event;

    while (open) {
        while (SDL_PollEvent(&event)) {
            switch (event.type) {
                case SDL_EVENT_QUIT:
                    open = false;
                    break;

                case SDL_EVENT_KEY_DOWN:
                    if (event.key.key == SDLK_ESCAPE) {
                        open = false;
                    }

                default:
                    break;
            }
        }

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        SDL_GL_SwapWindow(window);
    }

    SDL_DestroyWindow(window);
    SDL_GL_DestroyContext(ctx);
    SDL_Quit();
    spdlog::info("ASURA::Goodbye!");

    return 0;
}
```

Here is the CMakeLists.txt used for this, each dependency is simply compiled by using `add_subdirectory`, to me, it's basically magic.
```cmake
cmake_minimum_required(VERSION 3.28)
project(Asura VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic")

add_subdirectory(vendor/SDL)
add_subdirectory(vendor/spdlog)

include_directories(
    vendor/
    vendor/glad
)

find_package(OpenGL REQUIRED)

set(ASURA_LIBS
    OpenGL::GL
    SDL3::SDL3
    spdlog
)

set(ASURA_SRC
    src/main.cpp
)

set(DEP_SRC
    vendor/glad/glad.c
)

add_executable(Asura ${ASURA_SRC} ${DEP_SRC})

target_link_libraries(Asura ${ASURA_LIBS})

```

I'll just keep this here in case I need to come back for a starting SDL, OpenGL and C++ template.

This was a shorter post, but I've been travelling, so little is to be expected from the next few days. 

Next post, I'll probably explore some game engine architecture, and decide on the specifics of my game engine.

## Post 4 - (Re)Considering Graphics API's
### 10 January 2025
Today I'm reconsidering using just OpenGL. While it is an easy to use graphics API and is cross-platform, I would like to ship this engine and actually develop games in it, so some possibly personal sacrifices got to be made. I'm tossing up between [Sokol](https://github.com/floooh/sokol) with C++, or [BGFX](https://github.com/bkaradzic/bgfx) as a cross platform graphics API. They both abstract graphics API's and are wrappers for these API's. BGFX looks nice, and has been used for game engines before (see the repo's real-world uses), but I think I'll go for Sokol. Not a lot of thought behind this one, to be frank, but we'll give it our best shot. For simple stuff, it's a bit more code, such as clearing the background:\
Using OpenGL:
```cpp
gladLoadGLLoader((GLADloadproc)SDL_GL_GetProcAddress)
// in update/draw loop
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```
vs in sokol:
```cpp
sg_swapchain swapchain = {
    .width = screenWidth,
    .height = screenHeight,
};

sg_desc desc;
sg_setup(&desc);

pass_action = (sg_pass_action) {
    .colors[0] = {
        .load_action = SG_LOADACTION_CLEAR,
        .clear_value = { 1.0f, 0.0f, 0.0f, 1.0f }
    }
};
// in update/draw loop
sg_begin_pass((sg_pass){
    .action = pass_action,
    .swapchain = swapchain
});
sg_end_pass();
```
So yeah, it's *litte* bit more. While Sokol is pretty powerful and provides a really nice cross platform solution, I think that it's API is a little more confusing that I'd like, such as swapchains, passes, pipelines. Sticking with OpenGL is the best choice I believe. I was seriously considering-and had started writing code for-a Sokol based game engine, but I think sticking with industry-standard OpenGL is the best course of action here for reliable documentation, sheer number of tutorials and help online.

In the next post, I'll actually get to exploring game engine design and what to expect from Asura.

####  Resources:
- [Sokol](https://github.com/floooh/sokol)

## Post 5 - Game Engine Design
### 11 January 2025
There are countless ways to design a game engine, but ultimately, it boils down to *my* personal design philosophy. I tend to prefer game frameworks over traditional game engines. The distinction can be a bit nuanced, but here’s how I see it: a framework is code-only, where you call functions and interact with the framework’s API in the language it’s written in or a supported scripting language. Examples of popular game frameworks include [LÖVE](https://love2d.org/) and [Bevy](https://bevyengine.org/) (though I believe Bevy is planning to add an editor).  

A game engine, on the other hand, typically includes a user interface and a built-in editor. Some engines lean more toward programming, requiring developers to manually handle typical game functions, such as [Defold](https://defold.com/). Others handle much more for you, like [Unity](https://unity.com/) or [Unreal](https://www.unrealengine.com/en-US), offering scripting languages, visual scripting solutions, or both.  

Personally, I like having control over most systems in my games, shaping them however I see fit. That’s the goal for Asura: a non-intrusive game engine that provides freedom to write your own systems while offering assistance with the low-level stuff, like graphics APIs. I envision the engine opening a window with a game viewport, buttons, configuration settings, and the ability to create scripts that can be edited in the tool of your choice.  

As for the scripting language, I haven’t decided yet, but I’m leaning toward either Lua or C#. The aim is for one of my friends—or really anyone with little to no programming experience—to be able to create games using the engine in a simple language without worrying about memory management.

Much of this can be decided later, but it's good to have this written and decided on early, instead of thinking up a solution when I'm three-quarters the way through.

I also forgot to mention, much of this series is actually following the 'Game Engine Series' by The Cherno on YouTube, see the playlist [here](https://www.youtube.com/playlist?list=PLlrATfBNZ98dC-V-N3m0Go4deliWHPFwT)

While there will certainly be some similarities, I can almost gurantee that the final product will look nothing like The Cherno's engine (known as Hazel), I am simply using his series as it is a fantastic learning tool for game engine development.
