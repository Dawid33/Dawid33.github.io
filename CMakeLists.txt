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
    #set_target_properties(game PROPERTIES LINK_FLAGS "-o dist/engine.js -s USE_FREETYPE=1 -s DISABLE_EXCEPTION_CATCHING=0 -s DEMANGLE_SUPPORT=1 -s SAFE_HEAP=1 --bind -s WASM=1 -O2 -s LEGACY_GL_EMULATION=0  -s GL_UNSAFE_OPTS=0 --pre-js pre-module.js --post-js post-module.js -s ASSERTIONS=1 -s GL_ASSERTIONS=1 -s INVOKE_RUN=0  -std=c++11 -s USE_WEBGL2=1 -s FULL_ES3=1 -s USE_GLFW=3 -s OFFSCREENCANVAS_SUPPORT=1 --preload-file textures --preload-file shaders --preload-file fonts")
    target_link_options(game PUBLIC --shell-file ../index.html)
    set_target_properties(game PROPERTIES LINK_FLAGS "-s USE_GLFW=3 -s ASSERTIONS=1 -s WASM=1 -s ASYNCIFY")
endif()

target_link_libraries(game raylib m)
target_compile_options(game PRIVATE -Wall -Wextra)
target_include_directories(game PRIVATE ${raylib_SOURCE_DIRS}/include)