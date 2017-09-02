project(qr)
cmake_minimum_required(VERSION 2.6.0)

set(QR_VERSION "1.0.0")
set(QR_SOVERSION "1")

set(QR_COMMAND_SOURCES qrcmd.c)
set(QR_LIBRARY_SOURCES
    qr.c qrcnv.c qrcnv_bmp.c qrcnv_png.c qrcnv_svg.c qrcnv_tiff.c
)
set(QR_PUBLIC_HEADERS qr.h)

set(bindir bin)
set(incdir include)
set(libdir lib)

set(CMAKE_SKIP_BUILD_RPATH OFF)
set(CMAKE_BUILD_WITH_INSTALL_RPATH OFF)
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${libdir}")
set(CMAKE_INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/${libdir}")

##############################################################################
#find_package(ZLIB)
#set my own zlib path
set (ZLIB_INCLUDE_DIRS "")  #set in visual studio
set (ZLIB_LIBRARIES "")     #set in visual studio

#add_definitions(-Wall -Wextra)
# Set default compile flags for GCC
if(CMAKE_COMPILER_IS_GNUCXX)
 message(STATUS "GCC detected, adding compile flags")
 set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++98 -pedantic -Wall -Wextra")
endif(CMAKE_COMPILER_IS_GNUCXX)
##############################################################################

include_directories(${ZLIB_INCLUDE_DIRS})

add_executable(qrcmd ${QR_COMMAND_SOURCES})
add_executable(qrcmd_multi ${QR_COMMAND_SOURCES})

add_library(libqr_shared SHARED ${QR_LIBRARY_SOURCES})
add_library(libqr_static STATIC ${QR_LIBRARY_SOURCES})

target_link_libraries(qrcmd libqr_shared)
target_link_libraries(qrcmd_multi libqr_shared)
target_link_libraries(libqr_shared m ${ZLIB_LIBRARIES})

set_target_properties(qrcmd PROPERTIES
    OUTPUT_NAME qr
)
set_target_properties(qrcmd_multi PROPERTIES
    OUTPUT_NAME qrs
    COMPILE_FLAGS -DQRCMD_STRUCTURED_APPEND
)
set_target_properties(libqr_shared PROPERTIES
    OUTPUT_NAME qr
    VERSION ${QR_VERSION}
    SOVERSION ${QR_SOVERSION}
)
set_target_properties(libqr_static PROPERTIES
    OUTPUT_NAME qr
)

install(TARGETS qrcmd qrcmd_multi libqr_shared libqr_static
    RUNTIME DESTINATION ${bindir}
    LIBRARY DESTINATION ${libdir}
    ARCHIVE DESTINATION ${libdir}
)
install(FILES ${QR_PUBLIC_HEADERS} DESTINATION ${incdir})
