# SPDX-License-Identifier: CC0-1.0
# SPDX-FileCopyrightText: 2020 Lynn Kirby

cmake_minimum_required(VERSION 3.16)

if(COMMAND cmake_policy)
     cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

project(PractRand C CXX)

include(FindThreads)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

#
# Handle build options.
#

# Enable warnings by default if we're a standalone project. Disable them if
# we're built as a subdirectory.
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    set(enable_warnings ON)
else()
    set(enable_warnings OFF)
endif()

set(PRACTRAND_ENABLE_WARNINGS ${enable_warnings} CACHE BOOL "Enable warnings.")

add_library(practrand_build_config INTERFACE)

if(NOT PRACTRAND_ENABLE_WARNINGS)
    if(MSVC)
        # With MSVC, C4244 is being emitted from an STL include file even though
        # we're suppressing all errors with /w. clang-cl doesn't give the
        # warning but adding /wd4244 is safe.
        target_compile_options(practrand_build_config INTERFACE /w /wd4244)
    else()
        target_compile_options(practrand_build_config INTERFACE -w)
    endif()
endif()

#
# Main library.
#

add_library(
    practrand
    STATIC
    src/math.cpp
    src/non_uniform.cpp
    src/platform_specifics.cpp
    src/rand.cpp
    src/sha2.cpp
    src/test_batteries.cpp
    src/tests.cpp
    src/RNGs/arbee.cpp
    src/RNGs/chacha.cpp
    src/RNGs/efiix.cpp
    src/RNGs/hc256.cpp
    src/RNGs/isaac.cpp
    src/RNGs/jsf.cpp
    src/RNGs/mt19937.cpp
    src/RNGs/salsa.cpp
    src/RNGs/sfc.cpp
    src/RNGs/sha2_based_pool.cpp
    src/RNGs/trivium.cpp
    src/RNGs/xsm.cpp
    src/RNGs/other/fibonacci.cpp
    src/RNGs/other/indirection.cpp
    src/RNGs/other/mult.cpp
    src/RNGs/other/simple.cpp
    src/RNGs/other/transform.cpp)

target_include_directories(practrand PUBLIC include)
target_link_libraries(practrand PRIVATE practrand_build_config)

#
# Test executables.
#

foreach(exe IN ITEMS benchmark output test)
    add_executable(RNG_${exe} tools/RNG_${exe}.cpp)

    target_link_libraries(
        RNG_${exe}
        PRIVATE
        practrand_build_config
        practrand
        Threads::Threads)
endforeach()
