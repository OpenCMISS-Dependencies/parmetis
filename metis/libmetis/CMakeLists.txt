cmake_minimum_required (VERSION 3.0)
PROJECT(METIS VERSION 5.1 LANGUAGES C)
# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
add_library(metis ${metis_sources} $<TARGET_OBJECTS:gklib>)
set_target_properties(metis PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})
if (WITH_OPENMP)
    target_compile_definitions(metis PRIVATE OPENMP)
endif()
target_include_directories(metis PRIVATE ../GKlib)
target_include_directories(metis PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

if(UNIX)
  target_link_libraries(metis PUBLIC m)
endif()

install(TARGETS metis EXPORT metis-config
    DESTINATION lib
    INCLUDES DESTINATION include/metis-${METIS_VERSION})
install(TARGETS metis EXPORT parmetis-config
    DESTINATION lib
    INCLUDES DESTINATION include/metis-${METIS_VERSION})
file(GLOB headers "*.h")
install(FILES ${headers} ../include/metis.h DESTINATION include/metis-${METIS_VERSION})
install(EXPORT metis-config DESTINATION ${PACKAGE_CONFIG_DIR})
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake
    DESTINATION lib/cmake)