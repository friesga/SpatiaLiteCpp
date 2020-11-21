# ======================================================================
# Set minumum CMake version to be used
#
# Note: As a side effect this statement updates the policies in effect!
# ----------------------------------------------------------------------
cmake_minimum_required (VERSION 3.15)

# Enable use of MSVC_RUNTIME_LIBRARY
cmake_policy(SET CMP0091 NEW)

# ======================================================================
# Set the install prefix to build directory
# ----------------------------------------------------------------------
if (NOT DEFINED CMAKE_INSTALL_PREFIX)
    set (CMAKE_INSTALL_PREFIX
        "${CMAKE_BINARY_DIR}" CACHE INTERNAL
        "Prefix prepended to install directories" FORCE)
endif (NOT DEFINED CMAKE_INSTALL_PREFIX)

# ======================================================================
# Check for command line arguments
# ----------------------------------------------------------------------
option (SPATIALITECPP_USE_ICONV "Expect iconv use from spatialite" ON)
option (SPATIALITECPP_USE_LIBXML2 "Expect libxml2 from spatialite" ON)
option (SPATIALITECPP_BUILD_EXAMPLES "Build examples project" ON)
option (SPATIALITECPP_BUILD_DYNAMIC "Build as dynamic library" OFF)
option (SPATIALITECPP_BUILD_TEST "Build as test project" OFF)

# ======================================================================
# Define project
# ----------------------------------------------------------------------
project (SpatiaLiteCpp)

# ======================================================================
# Set SpatiaLiteCpp directory
# ----------------------------------------------------------------------
set (spatialitecpp_dir "${PROJECT_SOURCE_DIR}")
set (spatialitecpp_dir_inc "${spatialitecpp_dir}/include")

# ======================================================================
# Define (naming of) library versions
# ----------------------------------------------------------------------
set (SQLITE_STATIC_LIB SQLiteCpp-static)

# ======================================================================
# Find external libraries. Directories can be set by developer via
# command line arguments or environment variables. Default is the
# libraries are in 'ext', a submodule directory.
# ----------------------------------------------------------------------

# Set shared_ptr directory
set (sharedptr_dir_inc $ENV{sharedptr_dir_inc})
if (NOT sharedptr_dir_inc)
    set (sharedptr_dir_inc "${spatialitecpp_dir}/ext/shared_ptr/include")
endif ()

# Set SpatiaLite directory and libraries
# Allow env variable or top-level project to override
set (spatialite_dir_inc $ENV{spatialite_dir_inc})
if (NOT spatialite_dir_inc)
    if (WIN32)
        SET(spatialite_dir_inc "C:/Tools/spatialite-4.3.0a/include")
    else ()
        SET(spatialite_dir_inc "/usr/include")
    endif ()
endif ()

set (spatialite_dir_lib $ENV{spatialite_dir_lib})
if (NOT spatialite_dir_lib)
    if (WIN32)
        set (spatialite_dir_lib "C:/Tools/spatialite-4.3.0a/lib")
    else ()
        set (spatialite_dir_lib "/usr/lib/x86_64-linux-gnu")
    endif ()
endif ()

set (sqlite_dir_inc $ENV{sqlite_dir_inc})
if (NOT sqlite_dir_inc)
    if (WIN32)
        set (sqlite_dir_inc "C:/Tools/sqlite-3.32.3/include")
    else ()
        set (sqlite_dir_inc "/usr/include")
    endif ()
endif ()

set (sqlite_dir_lib $ENV{sqlite_dir_lib})
if (NOT sqlitelite_dir_lib)
    if (WIN32)
        set (sqlite_dir_lib "C:/Tools/sqlite-3.32.3/lib")
    else ()
        set (sqlite_dir_lib "/usr/lib/x86_64-linux-gnu")
    endif ()
endif ()

if (NOT spatialite_lib AND NOT sqlite3_lib)
    if (WIN32)
        set (spatialite_lib "${spatialite_dir_lib}/spatialite.lib")
        set (sqlite3_lib "${sqlite_dir_lib}/sqlite3.lib")
    else ()
        set (spatialite_lib "${spatialite_dir_lib}/libspatialite.so")
        set (sqlite3_lib "${sqlite_dir_lib}/libsqlite3.so")
    endif ()
endif ()

# Set SQLiteCpp directory and libraries
set (sqlitecpp_dir_inc $ENV{sqlitecpp_dir_inc})
if (NOT sqlitecpp_dir_inc)
    set (sqlitecpp_dir_inc "${spatialitecpp_dir}/ext/SQLiteCpp/include")
endif ()

# ======================================================================
# Define installation directory for all projects
# ----------------------------------------------------------------------
set (install_dir ".")

# ======================================================================
# Define library linking and flags for testing
# ----------------------------------------------------------------------
if (${SPATIALITECPP_BUILD_TEST})
    if (WIN32 AND NOT ${SPATIALITECPP_BUILD_DYNAMIC})
        set (CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
        set (CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
    endif ()
    if (CMAKE_COMPILER_IS_GNUCXX)
        if (${CMAKE_BUILD_TYPE} MATCHES "Debug")
            add_definitions (-fprofile-arcs -ftest-coverage)
            set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fprofile-arcs -ftest-coverage")
        endif ()
    endif ()
    add_subdirectory (${spatialitecpp_dir}/test)
endif ()

# ======================================================================
# Set output directories. Put the libraries and binaries that get built
# into directories at the top of the build tree.
#
# *** Don't move these commands below the add_* commands! ***
# ----------------------------------------------------------------------
set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
set (CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)

# ======================================================================
# Define project sub-directories
# ----------------------------------------------------------------------
add_subdirectory (${spatialitecpp_dir}/ext/SQLiteCppLocal)
add_subdirectory (${spatialitecpp_dir}/src)

#if (${SPATIALITECPP_BUILD_EXAMPLES})
    add_subdirectory (${spatialitecpp_dir}/examples)
#endif ()

# ======================================================================
# Define package installer
# ----------------------------------------------------------------------
include ("package.cmake")
