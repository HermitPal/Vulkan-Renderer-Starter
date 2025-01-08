## Vulkan Renderer Starter

This is an easy-to-start project for rendering with Vulkan.
You don't even need to have the Vulkan SDK installed.
Everything is fetched for you using CMake.

## What do I need?

You will need `glslangValidator`.

### Windows
1. Download `glslang-master-windows-Release.zip` from the [KhronosGroup glslang releases](https://github.com/KhronosGroup/glslang/releases).
2. Extract `glslangValidator.exe` to a location on your computer.
3. Add the path to `glslangValidator.exe` to your environment variables.

### Linux

```sh
sudo apt-get install glslang-tools
```

You will also need a C++17 compatible compiler and CMake version 3.20 or higher.

## Getting Started

1. Clone the repository:
    ```sh
    git clone https://github.com/HermitPal/Vulkan-Renderer-Starter.git
    cd Vulkan-Renderer-Starter
    ```

2. Create and navigate to the build directory:
    ```sh
    mkdir build
    cd build
    ```

3. Generate the build files using CMake:
    ```sh
    cmake ..
    ```

4. Build the project:
    ```sh
    cmake --build . --config Release
    ```

5. Your project will be located in the `build/bin` directory.


## References

main.cpp is from here: [Overv's Vulkan Guide](https://gist.github.com/Overv/7ac07356037592a121225172d7d78f2d).
