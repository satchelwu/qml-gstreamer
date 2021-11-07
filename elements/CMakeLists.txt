set(GST_PACKAGE_NAME "QtGStreamer" CACHE STRING "The name of this package")
set(GST_PACKAGE_ORIGIN "http://gstreamer.freedesktop.org/" CACHE STRING "The origin url of this package")

add_definitions(-DPACKAGE="qt-gstreamer"
                -DPACKAGE_NAME="${GST_PACKAGE_NAME}"
                -DPACKAGE_ORIGIN="${GST_PACKAGE_ORIGIN}"
                -DPACKAGE_VERSION="${QTGSTREAMER_VERSION}"
                -DGST_DISABLE_XML
                -DGST_DISABLE_LOADSAVE)

find_package(PkgConfig REQUIRED)
if (NOT ${PkgConfig_FOUND})
    message(FATAL_ERROR "pkg-config is required")
endif()

pkg_check_modules(GLIB REQUIRED IMPORTED_TARGET glib-2.0)
pkg_check_modules(GSTREAMER REQUIRED IMPORTED_TARGET gstreamer-1.0)
pkg_check_modules(GSTREAMER_BASE REQUIRED IMPORTED_TARGET gstreamer-base-1.0)
pkg_check_modules(GSTREAMER_VIDEO REQUIRED IMPORTED_TARGET gstreamer-video-1.0)


message(${GSTREAMER_LIBRARY_DIRS}/gstreamer-1.0)
if(NOT ${GSTREAMER_FOUND})
    message(FATAL_ERROR "gstreamer library is required")
endif()

if(NOT ${GSTREAMER_BASE_FOUND})
    message(FATAL_ERROR "gstreamer base library is required")
endif()

if(NOT ${GSTREAMER_VIDEO_FOUND})
    message(FATAL_ERROR "gstreamer video library is required")
endif()
include_directories(${GSTREAMER_INCLUDE_DIRS} ${GLIB2_INCLUDE_DIR})

if (${GSTREAMER_BASE_FOUND} AND ${GSTREAMER_VIDEO_FOUND})
    # message(${GSTREAMER_BASE_INCLUDE_DIRS} ${GSTREAMER_VIDEO_INCLUDE_DIRS})
    include_directories(${GSTREAMER_BASE_INCLUDE_DIRS} ${GSTREAMER_VIDEO_INCLUDE_DIRS})
    add_subdirectory(gstqtvideosink)
endif()
