# CMakeList.txt : CMake project for Tiny3DGS, include source and define
# project specific logic here.
#
cmake_minimum_required(VERSION 3.8)
project(Tiny3DGS VERSION 0.1.0 LANGUAGES CXX C)

include(FetchContent)

# Enable Hot Reload for MSVC compilers if supported.
if(POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

# Set source files for the executable.
add_executable(Tiny3DGS "main.cpp")

set_target_properties(Tiny3DGS PROPERTIES
  CXX_STANDARD 17
  CXX_EXTENSIONS OFF
  COMPILE_WARNING_AS_ERROR ON
)

# Compiler-specific options
if(MSVC)
  target_compile_options(Tiny3DGS PRIVATE /W4)
else()
  target_compile_options(Tiny3DGS PRIVATE -Wall -Wextra -pedantic)
endif()

# Xcode-specific options
if(XCODE)
  set_target_properties(Tiny3DGS PROPERTIES
    XCODE_GENERATE_SCHEME ON
    XCODE_SCHEME_ENABLE_GPU_FRAME_CAPTURE_MODE "Metal"
  )
endif()

# FetchContent for external dependencies
# WebGPU is loaded through Elie's git repo
# Allow both Webgpu-native.h and Dawn to be used.
# Default : webgpu-native
FetchContent_Declare(
  webgpu
  GIT_REPOSITORY https://github.com/eliemichel/WebGPU-distribution
  GIT_TAG main
)
FetchContent_MakeAvailable(webgpu)

FetchContent_Declare(
  glfw
  GIT_REPOSITORY https://github.com/glfw/glfw
  GIT_TAG master
)
FetchContent_MakeAvailable(glfw)

FetchContent_Declare(
  glm
  GIT_REPOSITORY https://github.com/g-truc/glm
  GIT_TAG master
)
FetchContent_MakeAvailable(glm)

# Link libraries
target_link_libraries(Tiny3DGS PRIVATE glfw webgpu)

#copy pre-computed files
target_copy_webgpu_binaries(Tiny3DGS)
