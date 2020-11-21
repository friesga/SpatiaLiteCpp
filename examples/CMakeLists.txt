
# ==================================================
# Set examples directory and files
# --------------------------------------------------
set (examples_dir_data "${spatialitecpp_dir}/examples")
set (examples_src
    "${CMAKE_CURRENT_SOURCE_DIR}/demos.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/examples.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/main.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/samples.cpp")

# ==================================================
# Add executable and link dependencies
# --------------------------------------------------

# Link as static library if dynamic option not set
if (WIN32 AND ${SPATIALITECPP_BUILD_DYNAMIC})
    add_definitions (-DSPATIALITECPP_IMPORT)
endif ()

add_executable (examples ${examples_src})
set_property (TARGET examples PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")

set (LIBS
    "C:/Tools/spatialite-4.3.0a/lib/spatialite.lib"
    "C:/Tools/sqlite-3.32.3/lib/sqlite3-static.lib"
    "C:/Tools/geos-3.8.1/lib/geos-static.lib"
    "C:/Tools/geos-3.8.1/lib/geos_c-static.lib"
    "C:/Tools/proj-7.1.0/lib/proj_7_1-static.lib"
    "C:/Tools/freexl-1.0.6/lib/freexl-static.lib"
    "C:/Tools/libxml2-2.9.10/lib/libxml2_a.lib"
    "C:/Tools/libiconv-1.16/lib/libiconvStatic.lib"
    "C:/Tools/zlib-1.2.11/lib/libz-static.lib"
    WS2_32.lib)

target_link_libraries (examples
    ${LIBS}
    SQLiteCpp
    SpatiaLiteCpp)

# ==================================================
# Set include directories
# --------------------------------------------------
set (examples_inc
    ${sharedptr_dir_inc}
    ${spatialitecpp_dir_inc}
    ${sqlitecpp_dir_inc}
    ${spatialite_dir_inc}
    ${sqlite_dir_inc})
include_directories (${examples_inc})

# ==================================================
# Add executable to install project
# --------------------------------------------------
set (install_dir_ex "${install_dir}/examples")

install (TARGETS examples RUNTIME DESTINATION ${install_dir_ex}/)
install (DIRECTORY "${examples_dir_data}/spatialite" DESTINATION ${install_dir_ex})
install (DIRECTORY "${examples_dir_data}/states" DESTINATION ${install_dir_ex})
install (DIRECTORY "${examples_dir_data}/wfs" DESTINATION ${install_dir_ex})
install (DIRECTORY "${examples_dir_data}/xml" DESTINATION ${install_dir_ex})

if (${SPATIALITECPP_BUILD_DYNAMIC})
    install (DIRECTORY "${install_dir}/lib/"
            DESTINATION ${install_dir_ex}
            FILES_MATCHING PATTERN "*${CMAKE_SHARED_LIBRARY_SUFFIX}")
endif ()

# ==================================================
# Copy example files
# --------------------------------------------------
set (examples_build_dir "${CMAKE_BINARY_DIR}/examples")

file (COPY "${examples_dir_data}/spatialite" DESTINATION ${examples_build_dir})
file (COPY "${examples_dir_data}/states" DESTINATION ${examples_build_dir})
file (COPY "${examples_dir_data}/wfs" DESTINATION ${examples_build_dir})
file (COPY "${examples_dir_data}/xml" DESTINATION ${examples_build_dir})
