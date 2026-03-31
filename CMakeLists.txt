cmake_minimum_required(VERSION 3.10)
project(print)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)
option(BUILD_TESTS "BUILD_TESTS" OFF)

add_library(print STATIC sources/print.cpp)

target_include_directories(print PUBLIC 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

if(BUILD_EXAMPLES)
    add_executable(example examples/example1.cpp)
    target_link_libraries(example print)
endif()

if(BUILD_TESTS)
    enable_testing()
    
    find_package(Threads REQUIRED) # Иногда это помогает gtest найти зависимости
    
    add_subdirectory(third-party/gtest)
    
    set_property(TARGET gtest PROPERTY CXX_STANDARD 17)
    set_property(TARGET gtest PROPERTY CXX_STANDARD_REQUIRED ON)
    set_property(TARGET gtest PROPERTY COMPILE_FLAGS "/MDd")
    
    add_executable(check tests/test1.cpp)
    target_link_libraries(check print gtest_main)
    
    target_include_directories(check PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)
    
    add_test(NAME check COMMAND check)
endif()
