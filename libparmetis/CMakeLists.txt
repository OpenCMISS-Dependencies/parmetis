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
#set_target_properties(parmetis PROPERTIES LINK_FLAGS "${MPI_LINK_FLAGS}")

install(TARGETS parmetis EXPORT parmetis-config
    DESTINATION lib
    INCLUDES DESTINATION include/parmetis-${PARMETIS_VERSION})
install(EXPORT parmetis-config DESTINATION lib/cmake)
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-version.cmake COMPATIBILITY AnyNewerVersion)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/parmetis-config-version.cmake 
    DESTINATION lib/cmake)