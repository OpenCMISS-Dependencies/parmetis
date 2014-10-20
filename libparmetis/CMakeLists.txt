# Find sources.
file(GLOB parmetis_sources *.c)

# Create libparmetis
add_library(parmetis STATIC ${parmetis_sources})
target_include_directories(parmetis PRIVATE . ../metis/GKlib ${MPI_C_INCLUDE_PATH} ${MPI_Fortran_INCLUDE_PATH})
#$<TARGET_PROPERTY:mpi,INTERFACE_INCLUDE_DIRECTORIES>
target_include_directories(parmetis PUBLIC ../include) 

get_property(outvar TARGET parmetis PROPERTY INCLUDE_DIRECTORIES)
message(STATUS "INCLUDE_DIRECTORIES of parmetis: ${outvar}")

# Link with metis and MPI libraries.
target_link_libraries(parmetis metis ${MPI_C_LIBRARIES} ${MPI_Fortran_LIBRARIES})
#set_target_properties(parmetis PROPERTIES LINK_FLAGS "${MPI_LINK_FLAGS}")

export(TARGETS parmetis FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/parmetis-config.cmake)