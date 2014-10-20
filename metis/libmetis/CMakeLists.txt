# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
add_library(metis STATIC ${GKlib_sources} ${metis_sources})
target_include_directories(metis PRIVATE ../GKlib .)
target_include_directories(metis PUBLIC ../include)

if(UNIX)
  target_link_libraries(metis m)
endif()
export(TARGETS metis FILE ${OPENCMISS_DEPENDENCIES_CONFIGS_DIR}/metis-config.cmake)