# Find sources.
file(GLOB parmetis_sources *.c)

# Create libparmetis
add_library(parmetis STATIC ${parmetis_sources})
target_include_directories(parmetis PRIVATE ../metis/GKlib ${MPI_C_INCLUDE_PATH} ${MPI_Fortran_INCLUDE_PATH})
#$<TARGET_PROPERTY:mpi,INTERFACE_INCLUDE_DIRECTORIES>
target_include_directories(parmetis PUBLIC ../include .)
target_compile_definitions(parmetis PRIVATE OPENMP)

# Link with metis and MPI libraries.
target_link_libraries(parmetis
    PUBLIC metis
    PRIVATE ${MPI_C_LIBRARIES} ${MPI_Fortran_LIBRARIES})
#set_target_properties(parmetis PROPERTIES LINK_FLAGS "${MPI_LINK_FLAGS}")

export(TARGETS parmetis metis gklib FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/parmetis-${PARMETIS_VERSION}/parmetis-config.cmake)
include(CMakePackageConfigHelpers)
WRITE_BASIC_PACKAGE_VERSION_FILE(${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/parmetis-${PARMETIS_VERSION}/parmetis-config-version.cmake
    VERSION ${PARMETIS_VERSION} COMPATIBILITY AnyNewerVersion)