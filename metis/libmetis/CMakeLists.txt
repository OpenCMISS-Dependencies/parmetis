cmake_minimum_required (VERSION 3.0)
PROJECT(METIS VERSION ${METIS_VERSION_GLOBAL} LANGUAGES C)
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
file(GLOB headers "*.h")
install(FILES ${headers} ../include/metis.h DESTINATION include/metis-${METIS_VERSION})
install(EXPORT metis-config DESTINATION ${PACKAGE_CONFIG_DIR})
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake COMPATIBILITY AnyNewerVersion)
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/metis-config-dependencies.cmake 
    "include(CMakeFindDependencyMacro)\r\n"
    	"set(${PROJECT_NAME}_IMPORT_PREFIX \${_IMPORT_PREFIX})\r\n")
if (WITH_OPENMP)
    file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/metis-config-dependencies.cmake "find_package(OpenMP)\r\n")
endif() 
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/metis-config-dependencies.cmake "set(_IMPORT_PREFIX \${${PROJECT_NAME}_IMPORT_PREFIX})")
install(FILES 
    ${CMAKE_CURRENT_BINARY_DIR}/metis-config-version.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/metis-config-dependencies.cmake
    DESTINATION ${PACKAGE_CONFIG_DIR})