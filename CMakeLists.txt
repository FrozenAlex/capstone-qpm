cmake_minimum_required(VERSION 3.22)

file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/ndkpath.txt" NDK_PATH LIMIT_COUNT 1)
file(TO_CMAKE_PATH "${NDK_PATH}" NDK_PATH)
set(CMAKE_TOOLCHAIN_FILE "${NDK_PATH}/build/cmake/android.toolchain.cmake")
set(ANDROID_ABI arm64-v8a)
set(ANDROID_PLATFORM 24)
project(capstone-qpm LANGUAGES C)

include(FetchContent)
# Upstream Capstone version; independent of the QPM package version.
FetchContent_Declare(capstone
    URL https://codeload.github.com/capstone-engine/capstone/tar.gz/refs/tags/5.0.8
    URL_HASH SHA256=467e128736227a12a2066c562cfe69a2b7c45353fab3f02a0522d9cd62ccf0f6
)

set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
set(BUILD_STATIC_LIBS ON CACHE BOOL "" FORCE)
set(CAPSTONE_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(CAPSTONE_BUILD_CSTOOL OFF CACHE BOOL "" FORCE)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
FetchContent_MakeAvailable(capstone)
set_target_properties(capstone_static PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
)
