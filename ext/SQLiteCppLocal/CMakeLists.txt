# 
# This CMake file is a local alternate to the one
# that comes with SQLiteC++. This build maintains
# consistent run-time libraries.
# 

# ==================================================
# Set external directories
# --------------------------------------------------
set (sqlitecpp_dir "${spatialitecpp_dir}/ext/SQLiteCpp")

# ==================================================
# Set header files
# --------------------------------------------------
set (sqlitecpp_hdr
    "${sqlitecpp_dir}/include/SQLiteCpp/Assertion.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Backup.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Column.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Database.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Exception.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/SQLiteCpp.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Statement.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Transaction.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/VariadicBind.h")

# ==================================================
# Set source files
# --------------------------------------------------
set (sqlitecpp_src
    "${sqlitecpp_dir}/src/Backup.cpp"
    "${sqlitecpp_dir}/src/Column.cpp"
    "${sqlitecpp_dir}/src/Database.cpp"
    "${sqlitecpp_dir}/src/Statement.cpp"
    "${sqlitecpp_dir}/src/Transaction.cpp")

# ==================================================
# Set include directories
# --------------------------------------------------
set (sqlitecpp_inc
    "${sqlitecpp_dir}/include"
    "${sqlitecpp_dir}/sqlite3")

include_directories (${sqlitecpp_inc})

# ==================================================
# Add executable and link dependencies
# --------------------------------------------------
if (UNIX)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif ()

# Build static library version
add_library (${SQLITE_STATIC_LIB} STATIC)
set_property (TARGET ${SQLITE_STATIC_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")

# Build dynamic library version
add_library (${SQLITE_SHARED_LIB} SHARED)
set_property (TARGET ${SQLITE_SHARED_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreadedDLL")

# Set sources for the targets
set_property (TARGET ${SQLITE_STATIC_LIB} ${SQLITE_SHARED_LIB}
    PROPERTY SOURCES ${sqlitecpp_src} ${sqlitecpp_hdr})

target_compile_options(${SQLITE_STATIC_LIB} PRIVATE $<$<CXX_COMPILER_ID:MSVC>:/nologo>)
target_compile_options(${SQLITE_SHARED_LIB} PRIVATE $<$<CXX_COMPILER_ID:MSVC>:/nologo>)

set_target_properties (${SQLITE_STATIC_LIB} PROPERTIES LINKER_LANGUAGE CXX)
set_target_properties (${SQLITE_SHARED_LIB} PROPERTIES LINKER_LANGUAGE CXX)

# Define libraries to link against
target_link_libraries (${SQLITE_SHARED_LIB} ${sqlite3_shared_lib})

if (UNIX)
    target_link_libraries (${SQLITE_STATIC_LIB} ${sqlite3_static_lib})
endif ()