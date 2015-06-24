# Find sources.
file(GLOB parmetis_sources *.c)

# Create libparmetis
add_library(parmetis ${parmetis_sources})
set_target_properties(parmetis PROPERTIES OUTPUT_NAME parmetis-${PARMETIS_VERSION})
target_include_directories(parmetis PRIVATE ${MPI_C_INCLUDE_PATH} ${MPI_Fortran_INCLUDE_PATH})
target_include_directories(parmetis PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../metis/GKlib>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)
if (WITH_OPENMP)
    target_compile_definitions(parmetis PRIVATE OPENMP)
endif()

# Link with metis and MPI libraries.
target_link_libraries(parmetis
    PUBLIC metis
    PRIVATE ${MPI_C_LIBRARIES} ${MPI_Fortran_LIBRARIES})

install(TARGETS parmetis EXPORT parmetis-config
    DESTINATION lib
    INCLUDES DESTINATION include/parmetis-${PARMETIS_VERSION})
install(EXPORT parmetis-config DESTINATION ${PACKAGE_CONFIG_DIR})
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-version.cmake COMPATIBILITY AnyNewerVersion)
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-dependencies.cmake 
    "include(CMakeFindDependencyMacro)\r\n"
    	"set(${PROJECT_NAME}_IMPORT_PREFIX \${_IMPORT_PREFIX})\r\n"
    "find_package(METIS ${METIS_VERSION_GLOBAL} REQUIRED)\r\n")
if (WITH_OPENMP)
    file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-dependencies.cmake "find_package(OpenMP)\r\n")
endif() 
file(APPEND ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-dependencies.cmake "set(_IMPORT_PREFIX \${${PROJECT_NAME}_IMPORT_PREFIX})")
install(FILES 
    ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-version.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-dependencies.cmake
    DESTINATION ${PACKAGE_CONFIG_DIR})