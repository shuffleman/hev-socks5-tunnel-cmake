cmake_minimum_required(VERSION 3.10)
project(yaml C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -pipe -Wno-unused-function")
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

add_definitions(
    -DYAML_VERSION_MAJOR=0
    -DYAML_VERSION_MINOR=2
    -DYAML_VERSION_PATCH=5
    -DYAML_VERSION_STRING="0.2.5"
)

file(GLOB YAML_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

add_library(yaml STATIC ${YAML_SOURCES})

set_target_properties(yaml PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin
)