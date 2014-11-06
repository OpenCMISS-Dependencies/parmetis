cmake_minimum_required(VERSION 3.0)
project(PARMETIS VERSION 4.0.3 LANGUAGES C)

option(BUILD_TESTS "${PROJECT_NAME} - Build tests" ON)

find_package(MPI REQUIRED)
SET(GKLIB_PATH ${PARMETIS_SOURCE_DIR}/metis/GKlib)
# Hotfix: parmetis wont build with optimization flags on
add_definitions(-O0)

# List of directories that cmake will look for CMakeLists.txt
add_subdirectory(metis/GKlib)
add_subdirectory(metis/libmetis)
add_subdirectory(libparmetis)

install(FILES include/parmetis.h DESTINATION include/parmetis-${PARMETIS_VERSION})

# testing / programs
if (BUILD_TESTS)
    add_subdirectory(metis/programs)
    add_subdirectory(programs)
endif()