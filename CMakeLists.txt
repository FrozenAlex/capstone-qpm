cmake_minimum_required(VERSION 3.22)

file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/ndkpath.txt" NDK_PATH LIMIT_COUNT 1)
file(TO_CMAKE_PATH "${NDK_PATH}" NDK_PATH)
set(CMAKE_TOOLCHAIN_FILE "${NDK_PATH}/build/cmake/android.toolchain.cmake")
set(ANDROID_ABI arm64-v8a)
set(ANDROID_PLATFORM 24)
project(capstone-qpm LANGUAGES C)

include(FetchContent)
# This revision matches the public headers shipped by capstone 0.1.0.
FetchContent_Populate(capstone
    URL https://codeload.github.com/capstone-engine/capstone/tar.gz/6ac6255afabd5ce3a469a1247e239398df320d17
    URL_HASH SHA256=038e2269810eafd0120193e896ec01acbd7ae65c34d7ab6243d8d48ab42a20f8
)

# The upstream revision predates CMake 4; patch only the downloaded build file.
file(READ "${capstone_SOURCE_DIR}/CMakeLists.txt" CAPSTONE_CMAKE)
string(REPLACE "SET CMP0048 OLD" "SET CMP0048 NEW" CAPSTONE_CMAKE "${CAPSTONE_CMAKE}")
file(WRITE "${capstone_SOURCE_DIR}/CMakeLists.txt" "${CAPSTONE_CMAKE}")
set(CMAKE_POLICY_VERSION_MINIMUM 3.5)

set(CAPSTONE_BUILD_SHARED OFF CACHE BOOL "" FORCE)
set(CAPSTONE_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(CAPSTONE_BUILD_CSTOOL OFF CACHE BOOL "" FORCE)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
add_subdirectory("${capstone_SOURCE_DIR}" "${capstone_BINARY_DIR}")
set_target_properties(capstone-static PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
)
