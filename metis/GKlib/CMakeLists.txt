cmake_minimum_required(VERSION 3.0)
project(GKlib VERSION 1 LANGUAGES C)

include(./GKlibSystem.cmake)

add_library(gklib OBJECT ${GKlib_sources})
target_include_directories(gklib PUBLIC .)

include_directories(test)
add_subdirectory(test)