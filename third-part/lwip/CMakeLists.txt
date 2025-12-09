cmake_minimum_required(VERSION 3.10)
project(lwip C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -pipe")

file(GLOB_RECURSE LWIP_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/src/include
    ${CMAKE_CURRENT_SOURCE_DIR}/src/ports/include
)

add_library(lwip STATIC ${LWIP_SOURCES})

set_target_properties(lwip PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin
)

target_link_libraries(lwip pthread)
