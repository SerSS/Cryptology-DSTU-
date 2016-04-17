# cmake_minimum_required(VERSION <specify CMake version here>)
project(kripto)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

set(SOURCE_FILES main.cpp)
add_executable(kripto ${SOURCE_FILES})