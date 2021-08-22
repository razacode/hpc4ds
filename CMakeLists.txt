cmake_minimum_required(VERSION 3.11)

# === Project
project(sudoku
        VERSION 0.0.1
        DESCRIPTION "Sudoku puzzles solver"
        LANGUAGES C)
# Configuration File
configure_file("${PROJECT_SOURCE_DIR}/config/config.h.in" "${PROJECT_SOURCE_DIR}/include/sp/config.h" @ONLY)

# === Include
include(FetchContent)

# === Dependencies
# argparse
FetchContent_Declare(
        argparse
        GIT_REPOSITORY https://github.com/cofyc/argparse.git
        GIT_TAG master
)
FetchContent_MakeAvailable(argparse)
include_directories("${argparse_SOURCE_DIR}")
# MPI
find_package(MPI REQUIRED)
include_directories(${MPI_INCLUDE_PATH})
# Open MP
find_package(OpenMP REQUIRED)

# === Configuration
set(CMAKE_C_STANDARD 11)
set(CMAKE_C_COMPILER mpicc)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Wshadow -Wdouble-promotion -Wformat -Wformat-security -Wundef -Wconversion -Wtype-limits -fno-common -pedantic")
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -g")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3")

# Include directories
include_directories("${PROJECT_SOURCE_DIR}/include")
include_directories("${PROJECT_SOURCE_DIR}/src")

# Headers
file(GLOB_RECURSE HEADER_FILES "${PROJECT_SOURCE_DIR}/include/*.h")
# Sources
file(GLOB_RECURSE SOURCE_FILES "${PROJECT_SOURCE_DIR}/src/*.c")

# Executable
add_executable(sudoku ${SOURCE_FILES} ${HEADER_FILES})
target_link_libraries(sudoku PRIVATE argparse OpenMP::OpenMP_C)

# Copy `inputs` folder
file(COPY inputs DESTINATION ${PROJECT_BINARY_DIR})