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
add_library (SQLiteCpp STATIC ${sqlitecpp_src} ${sqlitecpp_hdr})
set_property (TARGET SQLiteCpp PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")

set_target_properties (SQLiteCpp PROPERTIES LINKER_LANGUAGE CXX)

if (UNIX)
    target_link_libraries (SQLiteCpp ${sqlite3_lib})
endif ()