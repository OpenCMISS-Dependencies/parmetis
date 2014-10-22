# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
SET(METIS_VERSION 5.0)
add_library(metis5 STATIC ${metis_sources})
set_target_properties(metis5 PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})
target_include_directories(metis5 PRIVATE ../GKlib .)
target_include_directories(metis5 PUBLIC ../include)
target_link_libraries(metis5 PUBLIC gklib)

if(UNIX)
  target_link_libraries(metis5 PUBLIC m)
endif()

export(TARGETS metis5 gklib FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis5-config.cmake)
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-${METIS_VERSION}/metis5-config-version.cmake
    VERSION 5.0 COMPATIBILITY AnyNewerVersion)