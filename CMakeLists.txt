cmake_minimum_required(VERSION 3.0)
project(PARMETIS VERSION 4.0.3 LANGUAGES C)

option(BUILD_TESTS "${PROJECT_NAME} - Build tests" ON)
# Flag passed in to control use of openmp.
# Changed ENABLE_OPENMP in metislib.h and metisbin.h to _OPENMP to have standard compliant procedure
option(WITH_OPENMP "${PROJECT_NAME} - Use OpenMP" ON)

find_package(MPI REQUIRED)
SET(GKLIB_PATH ${PARMETIS_SOURCE_DIR}/metis/GKlib)
# Hotfix: parmetis wont build with optimization flags on
add_definitions(-O0)
# On MinGW there is no regex.h, so use gklib variant.
# This is correctly set in the GKLib CMakeLists, but not propagated to libmetis
# Also adding __MINGW__ definition for some code changes (gk_getopt.h && gk_arch.h)
if(MINGW)
    add_definitions(-DUSE_GKREGEX -D__MINGW__)
endif()

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