cmake_minimum_required(VERSION 3.0)
project(sdlarch C)

set(CMAKE_C_STANDARD 99)

set(target sdlarch)
set(sources sdlarch.c glad.c)
set(CFLAGS "-Wall -g")
if(APPLE)
    set(LFLAGS "-static-libstdc++")
else()
    set(LFLAGS "-static-libgcc")
endif()
set(LIBS "")
set(packages sdl2)

# Retrieve library flags, flags, and include directories from pkg-config
if(packages)
    execute_process(COMMAND pkg-config --libs-only-l ${packages}
            OUTPUT_VARIABLE pkgconfig_libs OUTPUT_STRIP_TRAILING_WHITESPACE)
    execute_process(COMMAND pkg-config --libs-only-L --libs-only-other ${packages}
            OUTPUT_VARIABLE pkgconfig_lflags OUTPUT_STRIP_TRAILING_WHITESPACE)
    execute_process(COMMAND pkg-config --cflags ${packages}
            OUTPUT_VARIABLE pkgconfig_cflags OUTPUT_STRIP_TRAILING_WHITESPACE)
    set(LIBS ${pkgconfig_libs})
    set(LFLAGS ${pkgconfig_lflags})
    set(CFLAGS "${CFLAGS} ${pkgconfig_cflags}")
endif()

# Define the output directory for object files
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/build)

# Create a target for the executable
add_executable(${target} ${sources})

# Link libraries and set flags
target_link_libraries(${target} ${LIBS})
set_target_properties(${target} PROPERTIES LINK_FLAGS ${LFLAGS})
set_target_properties(${target} PROPERTIES COMPILE_FLAGS ${CFLAGS})
