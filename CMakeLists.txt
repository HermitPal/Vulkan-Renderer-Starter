cmake_minimum_required(VERSION 3.20)
project(vulkan_project)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Fetch GLFW
include(FetchContent)
FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG 3.3.8
)
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)

# Fetch Vulkan Headers
FetchContent_Declare(
    vulkan_headers
    GIT_REPOSITORY https://github.com/KhronosGroup/Vulkan-Headers.git
    GIT_TAG v1.3.275
)

# Fetch Vulkan Loader
FetchContent_Declare(
    vulkan_loader
    GIT_REPOSITORY https://github.com/KhronosGroup/Vulkan-Loader.git
    GIT_TAG v1.3.275
)

# Make all content available
FetchContent_MakeAvailable(glfw vulkan_headers)

# Configure Vulkan Loader build
set(BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ENABLE_WERROR OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_XCB_SUPPORT OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_XLIB_SUPPORT OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_WAYLAND_SUPPORT OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(vulkan_loader)

# Add executable
add_executable(${PROJECT_NAME} main.cpp)

# Link libraries
target_link_libraries(${PROJECT_NAME} 
    PRIVATE 
        glfw
        vulkan
)

# Include directories
target_include_directories(${PROJECT_NAME}
    PRIVATE
        ${vulkan_headers_SOURCE_DIR}/include
)