cmake_minimum_required (VERSION 3.0)
PROJECT(METIS VERSION ${METIS_VERSION_GLOBAL} LANGUAGES C)

set(metis_sources
    auxapi.c
    balance.c
    bucketsort.c
    checkgraph.c
    coarsen.c
    compress.c
    contig.c
    debug.c
    fm.c
    fortran.c
    frename.c
    gklib.c
    graph.c
    initpart.c
    kmetis.c
    kwayfm.c
    kwayrefine.c
    mcutil.c
    mesh.c
    meshpart.c
    minconn.c
    mincover.c
    mmd.c
    ometis.c
    options.c
    parmetis.c
    pmetis.c
    refine.c
    separator.c
    sfm.c
    srefine.c
    stat.c
    timing.c
    util.c
    wspace.c
)

set(metis_headers
    defs.h
    gklib_defs.h
    gklib_rename.h
    macros.h
    metislib.h
    proto.h
    rename.h
    stdheaders.h
    struct.h
)

# Build libmetis.
add_library(metis ${metis_sources} ${metis_headers})
set_target_properties(metis PROPERTIES OUTPUT_NAME metis-${METIS_VERSION})
if (WITH_OPENMP)
    target_compile_definitions(metis PRIVATE OPENMP)
endif()
target_link_libraries(metis PUBLIC gklib)
target_include_directories(metis PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

if(UNIX)
  target_link_libraries(metis PUBLIC m)
endif()

install(TARGETS metis EXPORT metis-config
    DESTINATION lib
    INCLUDES DESTINATION include)
install(FILES ../include/metis.h DESTINATION include)
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
