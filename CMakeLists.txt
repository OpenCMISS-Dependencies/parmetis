cmake_minimum_required(VERSION 2.8)
project(ParMETIS C)

SET(CMAKE_PREFIX_PATH ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR} ${CMAKE_PREFIX_PATH})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OPENCMISS_DEPENDENCIES_LIBRARIES})

find_package(MPI REQUIRED)
#include(${GKLIB_PATH}/GKlibSystem.cmake)

# List of directories that cmake will look for CMakeLists.txt
add_subdirectory(metis/libmetis)
add_subdirectory(libparmetis)