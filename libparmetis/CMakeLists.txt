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
target_link_libraries(parmetis metis5 ${MPI_C_LIBRARIES} ${MPI_Fortran_LIBRARIES})
#TARGET_INCLUDE_DIRECTORIES(parmetis PRIVATE 
#        $<TARGET_PROPERTY:metis,INTERFACE_INCLUDE_DIRECTORIES>
#    )
#set_target_properties(parmetis PROPERTIES LINK_FLAGS "${MPI_LINK_FLAGS}")

export(TARGETS parmetis metis5 gklib FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/parmetis-config.cmake)
#export(TARGETS parmetis FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/parmetis-config.cmake)