cmake_minimum_required(VERSION 3.20)
project(raylib-game)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (${CMAKE_SYSTEM_NAME} MATCHES "Emscripten")
    #FILE(COPY_FILE ${PROJECT_SOURCE_DIR}/index.html ${PROJECT_BINARY_DIR}/index.html)
    set(CMAKE_EXECUTABLE_SUFFIX ".html")
    set(CMAKE_C_COMPILER "/lib/emscripten/emcc")
endif ()

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

if (${CMAKE_SYSTEM_NAME} MATCHES "Emscripten")
    target_link_options(game PUBLIC --shell-file ../index.html)
    set_target_properties(game PROPERTIES LINK_FLAGS "-s USE_GLFW=3 -s ASSERTIONS=1 -s WASM=1 -s ASYNCIFY")
    add_custom_command(
            TARGET game  POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_CURRENT_BINARY_DIR}/game.html
            ${PROJECT_SOURCE_DIR}/docs/game.html)
    add_custom_command(
            TARGET game  POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_CURRENT_BINARY_DIR}/game.js
            ${PROJECT_SOURCE_DIR}/docs/game.js)
    add_custom_command(
            TARGET game  POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_CURRENT_BINARY_DIR}/game.wasm
            ${PROJECT_SOURCE_DIR}/docs/game.wasm)
endif()

target_link_libraries(game raylib m)
target_compile_options(game PRIVATE -Wall -Wextra)
target_include_directories(game PRIVATE ${raylib_SOURCE_DIRS}/include)