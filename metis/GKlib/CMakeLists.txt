cmake_minimum_required(VERSION 3.0)
project(GKlib VERSION 1 LANGUAGES C)

# Helper modules.
include(CheckFunctionExists)
include(CheckIncludeFile)

# Setup options.
option(GDB "enable use of GDB" OFF)
option(ASSERT "turn asserts on" OFF)
option(ASSERT2 "additional assertions" OFF)
option(WITH_OPENMP "enable OpenMP support" ON)
option(PCRE "enable PCRE support" OFF)
option(GKREGEX "enable GKREGEX support" OFF)
option(GKRAND "enable GKRAND support" OFF)

# Add compiler flags.
if(MSVC)
  set(GKlib_DEFS WIN32 MSC _CRT_SECURE_NO_DEPRECATE USE_GKREGEX)
elseif(MINGW)
  set(GKlib_DEFS USE_GKREGEX)
else()
  set(GKlib_DEFS LINUX _FILE_OFFSET_BITS=64)
endif(MSVC)
if(CYGWIN)
  list(APPEND GKlib_DEFS CYGWIN)
endif()
if(CMAKE_COMPILER_IS_GNUCC)
    # GCC opts.
    set(GKlib_CFLAGS "${GKlib_CFLAGS} -std=c99 -fno-strict-aliasing")
    # GCC warnings.
    set(GKlib_CFLAGS "${GKlib_CFLAGS} -Wall -pedantic -Wno-unused-but-set-variable -Wno-unused-variable -Wno-unknown-pragmas")
elseif(${CMAKE_C_COMPILER_ID} MATCHES "Sun")
    # Sun insists on -xc99.
    set(GKlib_CFLAGS "${GKlib_CFLAGS} -xc99")
endif()

# Find OpenMP if it is requested.
if(WITH_OPENMP)
  find_package(OpenMP)
  if(OPENMP_FOUND)
    set(GKlib_COPTIONS "${GKlib_COPTIONS} -D__OPENMP__ ${OpenMP_C_FLAGS}")
  else()
    message(WARNING "OpenMP was requested but support was not found")
  endif()
endif()

# Add various definitions.
if(GDB)
  set(GKlib_COPTS "${GKlib_COPTS} -g")
  set(GKlib_COPTIONS "${GKlib_COPTIONS} -Werror")
endif(GDB)

if(GPROF)
  set(GKlib_COPTS "-pg")
endif(GPROF)

if(NOT ASSERT)
  list(APPEND GKlib_DEFS NDEBUG)
endif()

if(NOT ASSERT2)
  list(APPEND GKlib_DEFS NDEBUG2)
endif()

# Add various options
if(PCRE)
  list(APPEND GKlib_DEFS __WITHPCRE__)
endif()

if(GKREGEX)
  list(APPEND GKlib_DEFS USE_GKREGEX)
endif()

if(GKRAND)
  list(APPEND GKlib_DEFS USE_GKRAND)
endif()

# Check for features.
check_include_file(execinfo.h HAVE_EXECINFO_H)
if(HAVE_EXECINFO_H)
  list(APPEND GKlib_DEFS HAVE_EXECINFO_H)
endif(HAVE_EXECINFO_H)

check_function_exists(getline HAVE_GETLINE)
if(HAVE_GETLINE)
  list(APPEND GKlib_DEFS HAVE_GETLINE)
endif(HAVE_GETLINE)

# Custom check for TLS.
if(MSVC)
   list(APPEND GKlib_DEFS "__thread=__declspec(thread)")
else()
  # This if checks if that value is cached or not.
  if("${HAVE_THREADLOCALSTORAGE}" MATCHES "^${HAVE_THREADLOCALSTORAGE}$")
    try_compile(HAVE_THREADLOCALSTORAGE
      ${CMAKE_BINARY_DIR}
      ${GKLIB_PATH}/conf/check_thread_storage.c)
    if(HAVE_THREADLOCALSTORAGE)
      message(STATUS "checking for thread-local storage - found")
    else()
      message(STATUS "checking for thread-local storage - not found")
    endif()
  endif()
  if(NOT HAVE_THREADLOCALSTORAGE)
    list(APPEND GKlib_DEFS __thread=)
  endif()
endif()

# Find GKlib sources.
file(GLOB GKlib_sources ${GKLIB_PATH}/*.c)
file(GLOB GKlib_includes ${GKLIB_PATH}/*.h)

add_library(gklib ${GKlib_sources})
target_compile_definitions(gklib PUBLIC $<BUILD_INTERFACE:${GKlib_DEFS}>)
target_compile_options(gklib PUBLIC $<BUILD_INTERFACE:${GKlib_COPTIONS}>)
target_include_directories(gklib PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>)

install(TARGETS gklib EXPORT metis-config
    DESTINATION lib
)

include_directories(test)
add_subdirectory(test)