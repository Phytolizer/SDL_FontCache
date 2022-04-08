cmake_minimum_required(VERSION 3.1)
project(SDL_FontCache VERSION 0.10.0 LANGUAGES C)

find_package(sdl2 CONFIG REQUIRED)
find_package(sdl2-ttf CONFIG REQUIRED)

add_library(FontCache
    SDL_FontCache.h
    SDL_FontCache.c
)
target_include_directories(FontCache PUBLIC .)
target_link_libraries(FontCache PUBLIC SDL2::SDL2 SDL2::SDL2_ttf)

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

