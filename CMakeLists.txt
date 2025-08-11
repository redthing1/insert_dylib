cmake_minimum_required(VERSION 3.20)

project(insert_dylib
    VERSION 1.0.0
    DESCRIPTION "Command line utility for inserting a dylib load command into a Mach-O binary"
    LANGUAGES C
)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

add_executable(insert_dylib
    insert_dylib/main.c
)

if(APPLE)
    target_compile_definitions(insert_dylib PRIVATE
        _GNU_SOURCE
    )
    
    target_link_libraries(insert_dylib PRIVATE
        "-framework CoreFoundation"
    )
else()
    message(FATAL_ERROR "insert_dylib is only supported on macOS")
endif()

set_target_properties(insert_dylib PROPERTIES
    OUTPUT_NAME insert_dylib
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
)

target_compile_options(insert_dylib PRIVATE
    -Wall
    -Wextra
    -Wpedantic
    $<$<CONFIG:Debug>:-g -O0>
    $<$<CONFIG:Release>:-O2 -DNDEBUG>
)

install(TARGETS insert_dylib
    RUNTIME DESTINATION bin
)

enable_testing()

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/insert_dylib_tests/CMakeLists.txt")
    add_subdirectory(insert_dylib_tests EXCLUDE_FROM_ALL)
endif()