cmake_minimum_required(VERSION 3.10)
project(print)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)
option(BUILD_TESTS "Build tests" OFF)

# Создаем библиотеку print
add_library(print STATIC src/print.cpp)
target_include_directories(print PUBLIC 
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

# Примеры (опционально)
if(BUILD_EXAMPLES)
    add_executable(example examples/example1.cpp)
    target_link_libraries(example print)
endif()

# Тесты
if(BUILD_TESTS)
    enable_testing()
    
    # Добавляем GoogleTest из подмодуля
    add_subdirectory(third-party/gtest)
    
    # Добавляем тесты
    file(GLOB TEST_SOURCES tests/*.cpp)
    add_executable(check ${TEST_SOURCES})
    target_link_libraries(check print gtest_main)
    target_include_directories(check PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)
    
    add_test(NAME check COMMAND check)
endif()
