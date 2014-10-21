cmake_minimum_required(VERSION 2.8)
project(GKlib)

include(./GKlibSystem.cmake)

add_library(gklib STATIC ${GKlib_sources})
target_include_directories(gklib PUBLIC .)
if(UNIX)
  target_link_libraries(gklib m)
endif(UNIX)

include_directories("test")
add_subdirectory("test")