cmake_minimum_required(VERSION 3.10)
project(hev-socks5-tunnel C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O3 -pipe -Wall -Werror")

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

option(ENABLE_DEBUG "Enable debug mode" OFF)
option(ENABLE_STATIC "Enable static linking" OFF)
option(ENABLE_LIBRARY "Build as library" OFF)

if(ENABLE_DEBUG)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -O0 -DENABLE_DEBUG")
endif()

execute_process(
    COMMAND git rev-parse --short HEAD
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    OUTPUT_VARIABLE REV_ID
    OUTPUT_STRIP_TRAILING_WHITESPACE
    ERROR_QUIET
)

if(NOT REV_ID)
    set(REV_ID "unknown")
endif()

add_definitions(-DCOMMIT_ID="${REV_ID}")

if(ENABLE_LIBRARY)
    add_definitions(-DENABLE_LIBRARY)
endif()

include_directories(
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_SOURCE_DIR}/src/misc
    ${CMAKE_SOURCE_DIR}/src/core/include
    ${CMAKE_SOURCE_DIR}/third-part/yaml/include
    ${CMAKE_SOURCE_DIR}/third-part/wintun/include
    ${CMAKE_SOURCE_DIR}/third-part/lwip/src/include
    ${CMAKE_SOURCE_DIR}/third-part/lwip/src/ports/include
    ${CMAKE_SOURCE_DIR}/third-part/hev-task-system/include
)

add_subdirectory(third-part/yaml)
add_subdirectory(third-part/lwip)
add_subdirectory(third-part/hev-task-system)

file(GLOB_RECURSE SOURCES
    ${CMAKE_SOURCE_DIR}/src/*.c
)

add_executable(${PROJECT_NAME} ${SOURCES})

target_link_libraries(${PROJECT_NAME}
    yaml
    lwip
    hev-task-system
    pthread
)

if(MSYS)
    target_link_libraries(${PROJECT_NAME} msys-2.0 ws2_32 Iphlpapi)
endif()

if(ENABLE_STATIC)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static")
endif()

install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION bin
)

install(FILES ${CMAKE_SOURCE_DIR}/conf/main.yml
    DESTINATION etc
    RENAME ${PROJECT_NAME}.yml
)

add_library(${PROJECT_NAME}-static STATIC ${SOURCES})
set_target_properties(${PROJECT_NAME}-static PROPERTIES OUTPUT_NAME ${PROJECT_NAME})
target_link_libraries(${PROJECT_NAME}-static yaml lwip hev-task-system pthread)

add_library(${PROJECT_NAME}-shared SHARED ${SOURCES})
set_target_properties(${PROJECT_NAME}-shared PROPERTIES 
    OUTPUT_NAME ${PROJECT_NAME}
    POSITION_INDEPENDENT_CODE ON
)
target_link_libraries(${PROJECT_NAME}-shared yaml lwip hev-task-system pthread)
