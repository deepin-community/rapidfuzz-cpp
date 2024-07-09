# This is an amalgam of the rapidfuzz top-level CMakeLists.txt and the
# test one.

# Cmake config largely taken from catch2
cmake_minimum_required(VERSION 3.5)

if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.24)
  cmake_policy(SET CMP0135 NEW)
endif()

project(rapidfuzz-test LANGUAGES CXX VERSION 3.0.0)

# Turn on the verbose
set(CMAKE_VERBOSE_MAKEFILE ON)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(CMAKE_SYSTEM_PREFIX_PATH "/usr")
find_package(rapidfuzz REQUIRED)
find_package(Catch2 3 REQUIRED)

add_library(rapidfuzz-test INTERFACE)

target_compile_features(rapidfuzz-test INTERFACE cxx_std_17)

target_include_directories(rapidfuzz-test
    INTERFACE
      $<BUILD_INTERFACE:${SOURCES_DIR}/..>
)

include(CTest)
enable_testing()

function(rapidfuzz_add_test test)
    add_executable(test_${test} tests-${test}.cpp)
    target_link_libraries(test_${test} ${PROJECT_NAME})
    target_link_libraries(test_${test} Catch2::Catch2WithMain)
    if (RAPIDFUZZ_ENABLE_LINTERS)
        target_link_libraries(test_${test} project_warnings)
    endif()
    add_test(NAME ${test} COMMAND test_${test})
endfunction()

rapidfuzz_add_test(fuzz)
rapidfuzz_add_test(common)

add_subdirectory(distance)
