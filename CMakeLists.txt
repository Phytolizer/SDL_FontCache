cmake_minimum_required(VERSION 3.1)
project(SDL_FontCache VERSION 0.10.0 LANGUAGES C)

include(cmake/CPM.cmake)

option(BUILD_SHARED_LIBS OFF)

find_package(sdl2 CONFIG)
if(NOT sdl2_FOUND)
    CPMAddPackage(
        NAME SDL2
        GITHUB_REPOSITORY libsdl-org/SDL
        GIT_TAG release-2.0.20
    )
endif()
if(WIN32)
    CPMAddPackage(
        NAME FreeType
        GIT_REPOSITORY https://gitlab.freedesktop.org/freetype/freetype.git
        GIT_TAG VER-2-12-0
    )
    add_library(Freetype::Freetype ALIAS freetype)
endif()
find_package(sdl2-ttf CONFIG)
if(NOT sdl2-ttf_FOUND)
    CPMAddPackage(
        NAME SDL2_ttf
        GITHUB_REPOSITORY libsdl-org/SDL_ttf
        GIT_TAG main
    )
    add_library(SDL2::SDL2_ttf ALIAS SDL2_ttf)
endif()

add_library(FontCache
    SDL_FontCache.h
    SDL_FontCache.c
)
target_include_directories(FontCache PUBLIC .)
if(TARGET SDL2::SDL2-static)
    target_link_libraries(FontCache PUBLIC SDL2::SDL2-static)
else()
    target_link_libraries(FontCache PUBLIC SDL2::SDL2)
endif()
target_link_libraries(FontCache PUBLIC SDL2::SDL2_ttf)

add_library(FontCache::FontCache ALIAS FontCache)

add_executable(FontCache-tests
	test/main.c
)
file(COPY test/utf8_sample.txt DESTINATION ${CMAKE_BINARY_DIR})
file(COPY test/fonts/FreeSans.ttf DESTINATION ${CMAKE_BINARY_DIR}/fonts)
target_link_libraries(FontCache-tests FontCache SDL2::SDL2main)

if(UNIX)
    target_link_libraries(FontCache-tests X11 Xext)
endif()

