# Game Engine Development From Scratch
[Link to the engine's repository](https://github.com/shreejitmurthy/AsuraEngine)

## About
This one singular page serves as the sprawling thoughts of Shree (me) as I attempt to develop a game engine from scratch.

## Introduction
### Post 1
#### Monday, 6 January 2024
So, why? Why am I making a game engine? It’s a daunting task, no doubt—very few get past the initial stages of engine development. So, why am I doing this? Well, it’s simple: I love the idea of it. The phrase "*from scratch*" just sounds awesome, and the thought of building something from the ground up and watching it grow is something I truly yearn for.

I also see this as a fantastic learning opportunity. Most of my projects have been around ~5k lines of code, so managing a larger project like a game engine will give me valuable skills that (I believe) are useful in the industry. Plus, I just love games and game development. That said, I’m a terrible game developer and can never seem to finish a game. I’ve made games from scratch before, and I’m currently working on one in OpenGL and C++. I think this project will also prepare me well for my upcoming university degree, which starts soon.

Since finishing 12th grade in 2024, I’ve had a lot of free time—or at least I did, since much of it went into relaxing after a grueling year of studies. Now, I’m channeling that time into this project.

At the end of development (if there ever *is* an end), I hope to have a functioning engine capable of creating both 2D and 3D games. Honestly, any 3D engine can make a 2D game, since graphics APIs work in 3D by default, but I’d love for my engine to handle both seamlessly.

This series of blog posts is primarily to keep myself motivated, but it might also help me learn from my mistakes—and who knows? Maybe it’ll serve as a little showcase for future employers. My programming knowledge is entirely self-taught, so I’m not entirely sure about best industry practices yet. However, with my computing degree starting soon, I’m confident my code will gradually become more professional and aligned with industry standards over time.

All external references (i.e., libraries) will be provided as hyperlinks, linking directly to their main pages.


## Core Development
### Post 2
#### Monday, 6 January 2024
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
```c++
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
