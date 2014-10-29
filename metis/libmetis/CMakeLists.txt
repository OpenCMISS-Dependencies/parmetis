cmake_minimum_required (VERSION 3.0)
PROJECT(METIS VERSION 5.1 LANGUAGES C)
# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
add_library(metis STATIC ${metis_sources})
set_target_properties(metis PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})
target_compile_definitions(metis PRIVATE OPENMP)
target_include_directories(metis PRIVATE ../GKlib)
target_include_directories(metis PUBLIC . ../include)
target_link_libraries(metis PUBLIC gklib)

if(UNIX)
  target_link_libraries(metis PUBLIC m)
endif()

export(TARGETS metis gklib FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis-config.cmake)
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis-config-version.cmake
    VERSION ${METIS_VERSION} COMPATIBILITY AnyNewerVersion)