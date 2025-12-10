cmake_minimum_required(VERSION 3.10)
project(hev-task-system C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -pipe -Wall -Wno-unused-function")
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

add_definitions(
    -DENABLE_STACK_OVERFLOW_DETECTION
    -DENABLE_MEMALLOC_SLICE
    -DENABLE_IO_SPLICE_SYSCALL
    -DCONFIG_STACK_BACKEND=STACK_MMAP
    -DCONFIG_STACK_OVERFLOW_DETECTION=1
    -DCONFIG_MEMALLOC_SLICE_ALIGN=64
    -DCONFIG_MEMALLOC_SLICE_MAX_SIZE=4096
    -DCONFIG_MEMALLOC_SLICE_MAX_COUNT=1000
    -DCONFIG_SCHED_CLOCK=CLOCK_NONE
)

file(GLOB_RECURSE HEV_TASK_SOURCES 
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.S
)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

add_library(hev-task-system STATIC ${HEV_TASK_SOURCES})

set_target_properties(hev-task-system PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin
)

target_link_libraries(hev-task-system pthread)

if(MSYS)
    target_link_libraries(hev-task-system msys-2.0 ws2_32)
endif()