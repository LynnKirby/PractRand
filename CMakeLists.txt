cmake_minimum_required(VERSION 3.16)

if(COMMAND cmake_policy)
     cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

project(PractRand C CXX)

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
    src/RNGs/other/transform.cpp
)

target_include_directories(practrand PUBLIC include)

add_executable(RNG_benchmark tools/RNG_benchmark.cpp)
target_link_libraries(RNG_benchmark PRIVATE practrand pthread)

add_executable(RNG_output tools/RNG_output.cpp)
target_link_libraries(RNG_output PRIVATE practrand pthread)

add_executable(RNG_test tools/RNG_test.cpp)
target_link_libraries(RNG_test PRIVATE practrand pthread)
