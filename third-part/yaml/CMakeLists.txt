cmake_minimum_required(VERSION 3.10)
project(yaml C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -pipe")

file(GLOB YAML_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

add_library(yaml STATIC ${YAML_SOURCES})

set_target_properties(yaml PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin
)
