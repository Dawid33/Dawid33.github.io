cmake_minimum_required(VERSION 3.20)
project(raylib-game)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

include(FetchContent)

FetchContent_Declare(
  RAYLIB
  GIT_REPOSITORY "https://github.com/raysan5/raylib"
  GIT_TAG 4.0.0
)

FetchContent_GetProperties(RAYLIB)
if (NOT raylib_POPULATED)
    FetchContent_Populate(RAYLIB)
    add_subdirectory(${raylib_SOURCE_DIR} ${raylib_BINARY_DIR})
endif ()

add_executable(game
    main.cpp)

target_link_libraries(game raylib m)
target_compile_options(game PRIVATE -Wall -Wextra)
target_include_directories(game PRIVATE ${raylib_SOURCE_DIRS}/include)