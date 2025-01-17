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

# Fetch GLM
FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_TAG 0.9.9.8
)

# Make all content available
FetchContent_MakeAvailable(glfw vulkan_headers glm)

# Configure Vulkan Loader build
set(BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ENABLE_WERROR OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_XCB_SUPPORT OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_XLIB_SUPPORT OFF CACHE BOOL "" FORCE)
set(BUILD_WSI_WAYLAND_SUPPORT OFF CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(vulkan_loader)

# Set output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

foreach(OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES})
    string(TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR}/bin)
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR}/bin)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR}/bin)
endforeach()

set(SHADER_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin/shaders)

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
        ${glm_SOURCE_DIR}
)

# Compile shaders
file(GLOB SHADERS "shaders/*.vert" "shaders/*.frag")
foreach(SHADER ${SHADERS})
    get_filename_component(FILE_NAME_WE ${SHADER} NAME_WE)
    add_custom_command(
        OUTPUT ${SHADER_OUTPUT_DIRECTORY}/${FILE_NAME_WE}.spv
        COMMAND ${CMAKE_COMMAND} -E make_directory ${SHADER_OUTPUT_DIRECTORY}
        COMMAND glslangValidator -V ${SHADER} -o ${SHADER_OUTPUT_DIRECTORY}/${FILE_NAME_WE}.spv
        DEPENDS ${SHADER}
    )
    list(APPEND SPV_SHADERS ${SHADER_OUTPUT_DIRECTORY}/${FILE_NAME_WE}.spv)
endforeach()

add_custom_target(ShadersTarget DEPENDS ${SPV_SHADERS})
add_dependencies(${PROJECT_NAME} ShadersTarget)