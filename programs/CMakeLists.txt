include_directories(.)
# Create programs.
add_executable(ptest ptest.c io.c adaptgraph.c)
add_executable(mtest mtest.c io.c)
add_executable(parmetis_prog parmetis.c io.c adaptgraph.c)
set_target_properties(parmetis_prog PROPERTIES OUTPUT_NAME parmetis)
add_executable(pometis pometis.c io.c)

# Link to libparmetis.
foreach(prog ptest mtest parmetis_prog pometis)
  target_include_directories(${prog} PRIVATE ${MPI_C_INCLUDE_PATH} ${MPI_Fortran_INCLUDE_PATH})
  target_link_libraries(${prog} PRIVATE parmetis ${MPI_C_LIBRARIES} ${MPI_Fortran_LIBRARIES})
endforeach(prog)