cmake_minimum_required(VERSION 2.8)
project(ParMETIS C)

if (OPENCMISS_DEPENDENCIES_LIBRARIES)
    SET(CMAKE_PREFIX_PATH ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR} ${CMAKE_PREFIX_PATH})
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OPENCMISS_DEPENDENCIES_LIBRARIES})
else()
    SET(CMAKE_PREFIX_PATH ../cmake ${CMAKE_PREFIX_PATH})
    SET(OPENCMISS_DEPENDENCIES_CONFIGS_DIR ${CMAKE_CURRENT_BINARY_DIR})
    SET(BUILD_TESTING ON)
endif()

find_package(MPI REQUIRED)
SET(GKLIB_PATH ${ParMETIS_SOURCE_DIR}/metis/GKlib)
#include(${GKLIB_PATH}/GKlibSystem.cmake)

# List of directories that cmake will look for CMakeLists.txt
add_subdirectory(metis/GKlib)
add_subdirectory(metis/libmetis)
add_subdirectory(libparmetis)

# testing / programs
if (BUILD_TESTING)
    add_subdirectory(metis/programs)
    add_subdirectory(programs)
endif()