# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
add_library(metis STATIC ${metis_sources})
target_include_directories(metis PRIVATE ../GKlib .)
target_include_directories(metis PUBLIC ../include)
target_link_libraries(metis PUBLIC gklib)

if(UNIX)
  target_link_libraries(metis PUBLIC m)
endif()
export(TARGETS metis gklib FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-config.cmake)