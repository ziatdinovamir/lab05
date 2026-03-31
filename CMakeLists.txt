cmake_minimum_required(VERSION 3.10)
project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)
option(BUILD_TESTS "Build tests" OFF)

# Основная библиотека
add_library(print STATIC src/print.cpp)
target_include_directories(print PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

if(BUILD_EXAMPLES)
  add_executable(example examples/example1.cpp)
  target_link_libraries(example print)
endif()

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  
  file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check print gtest_main)
  add_test(NAME check COMMAND check)
endif()
