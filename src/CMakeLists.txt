
# ==================================================
# Set header files
# --------------------------------------------------
set (spatialitecpp_hdr 
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Auxiliary.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Blob.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Buffer.hpp"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Checksum.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/DynamicLine.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/ExifTagList.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/GeometryCollection.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/LineString.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/OutputBuffer.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Point.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Polygon.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/Ring.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatialDatabase.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatiaLiteCpp.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/SpatiaLiteCppAbi.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/VectorLayersList.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/WfsCatalog.h"
    "${spatialitecpp_dir}/include/SpatiaLiteCpp/WfsSchema.h")

# ==================================================
# Set source files
# --------------------------------------------------
set (spatialitecpp_src
    "${spatialitecpp_dir}/src/Auxiliary.cpp"
    "${spatialitecpp_dir}/src/Blob.cpp"
    "${spatialitecpp_dir}/src/Checksum.cpp"
    "${spatialitecpp_dir}/src/DynamicLine.cpp"
    "${spatialitecpp_dir}/src/ExifTagList.cpp"
    "${spatialitecpp_dir}/src/GeometryCollection.cpp"
    "${spatialitecpp_dir}/src/LineString.cpp"
    "${spatialitecpp_dir}/src/OutputBuffer.cpp"
    "${spatialitecpp_dir}/src/Point.cpp"
    "${spatialitecpp_dir}/src/Polygon.cpp"
    "${spatialitecpp_dir}/src/Ring.cpp"
    "${spatialitecpp_dir}/src/SpatialDatabase.cpp"
    "${spatialitecpp_dir}/src/VectorLayersList.cpp"
    "${spatialitecpp_dir}/src/WfsCatalog.cpp"
    "${spatialitecpp_dir}/src/WfsSchema.cpp")

# ==================================================
# Set xml2 specific files
# --------------------------------------------------
if (${SPATIALITECPP_USE_LIBXML2})
    list (APPEND spatialitecpp_hdr
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/XmlBlob.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/XmlDocument.h")
    list (APPEND spatialitecpp_src
        "${spatialitecpp_dir}/src/XmlBlob.cpp"
        "${spatialitecpp_dir}/src/XmlDocument.cpp")
endif ()

# ==================================================
# Set iconv specific files
# --------------------------------------------------
if (${SPATIALITECPP_USE_ICONV})
    list (APPEND spatialitecpp_hdr
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/Converter.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/Dbf.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/DbfField.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/DbfList.h"
        "${spatialitecpp_dir}/include/SpatiaLiteCpp/Shapefile.h")
    list (APPEND spatialitecpp_src
        "${spatialitecpp_dir}/src/Converter.cpp"
        "${spatialitecpp_dir}/src/Dbf.cpp"
        "${spatialitecpp_dir}/src/DbfField.cpp"
        "${spatialitecpp_dir}/src/DbfList.cpp"
        "${spatialitecpp_dir}/src/Shapefile.cpp")
endif ()

# ==================================================
# Set documentation files
# --------------------------------------------------
set (spatialitecpp_doc
    "${spatialitecpp_dir}/readme.md")

source_group ("Document Files" FILES ${spatialitecpp_doc})

# ==================================================
# Set script files
# --------------------------------------------------
set (spatialitecpp_script
    "${spatialitecpp_dir}/build.bat"
    "${spatialitecpp_dir}/build.sh"
    "${spatialitecpp_dir}/Doxyfile")

source_group ("Script Files" FILES ${spatialitecpp_script})

# ==================================================
# Set include directories
# --------------------------------------------------
set (spatialitecpp_inc
    ${sharedptr_dir_inc}
    ${spatialitecpp_dir_inc}
    ${spatialite_dir_inc}
    ${sqlitecpp_dir_inc}
    ${sqlite_dir_inc})
include_directories (${spatialitecpp_inc})

# ==================================================
# Add library and link dependencies
# --------------------------------------------------
if (${SPATIALITECPP_BUILD_DYNAMIC})
    if (WIN32)
        add_definitions (-DSPATIALITECPP_EXPORT)
    endif ()
    add_library (SpatiaLiteCpp SHARED ${spatialitecpp_src}
                                     ${spatialitecpp_hdr}
                                     ${spatialitecpp_doc}
                                     ${spatialitecpp_script})
    target_link_libraries (SpatiaLiteCpp
        SQLiteCpp
        ${spatialite_lib}
        ${sqlite3_static_lib})
else ()
    add_library (${SPATIALITE_STATIC_LIB} STATIC ${spatialitecpp_src}
        ${spatialitecpp_hdr} ${spatialitecpp_doc} ${spatialitecpp_script})
    set_property (TARGET ${SPATIALITE_STATIC_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")
ENDIF()

set_target_properties (${SPATIALITE_STATIC_LIB} PROPERTIES LINKER_LANGUAGE CXX)
target_compile_options(${SPATIALITE_STATIC_LIB} PRIVATE $<$<CXX_COMPILER_ID:MSVC>:/nologo>)

# ==================================================
# Install library and header files
# --------------------------------------------------
if (NOT CMAKE_LIBRARY_OUTPUT_DIRECTORY)
    set (install_dir_lib "${install_dir}/lib")
else ()
    set (install_dir_lib "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}")
endif ()

if (${SPATIALITECPP_BUILD_DYNAMIC})
    install (TARGETS SpatiaLiteCpp
        ARCHIVE DESTINATION ${install_dir_lib}/
        LIBRARY DESTINATION ${install_dir_lib}/
        RUNTIME DESTINATION ${install_dir_lib}/)
else ()
    install (TARGETS ${SPATIALITE_STATIC_LIB}
        ARCHIVE DESTINATION ${install_dir_lib}/)
ENDIF()

install (DIRECTORY "${spatialitecpp_dir_inc}" DESTINATION ${install_dir})
