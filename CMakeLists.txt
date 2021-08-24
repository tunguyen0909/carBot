cmake_minimum_required(VERSION 3.10)

project(robot)

add_definitions(-std=c++11)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Debug)

find_package(CUDA REQUIRED)

include_directories(${PROJECT_SOURCE_DIR}/include)
# include and link dirs of cuda and tensorrt, you need adapt them if yours are different
# cuda
include_directories(/usr/local/cuda/include)
link_directories(/usr/local/cuda/lib64)
# tensorrt
include_directories(/usr/include/x86_64-linux-gnu/)
link_directories(/usr/lib/x86_64-linux-gnu/)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Ofast -Wfatal-errors -D_MWAITXINTRIN_H_INCLUDED")
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")

cuda_add_library(myplugins SHARED ${PROJECT_SOURCE_DIR}/yololayer.cu)
target_link_libraries(myplugins nvinfer cudart)

find_package(OpenCV)
include_directories(OpenCV_INCLUDE_DIRS ${PROJECT_SOURCE_DIR}/src/)

file(GLOB SRC_FILES ${PROJECT_SOURCE_DIR}/src/*.*)
add_executable(robot ${PROJECT_SOURCE_DIR}/main.cpp ${SRC_FILES})

target_link_libraries(robot nvinfer cudart myplugins ${OpenCV_LIBS})

add_definitions(-O2 -pthread)

