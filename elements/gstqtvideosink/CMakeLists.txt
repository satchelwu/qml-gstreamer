# glib2_genmarshal(gstqtvideosinkmarshal
#     VOID:POINTER,FLOAT,FLOAT,FLOAT,FLOAT
#     VOID:POINTER,DOUBLE,DOUBLE,DOUBLE,DOUBLE
#     POINTER:POINTER,FLOAT,FLOAT,FLOAT,FLOAT
#     POINTER:POINTER,DOUBLE,DOUBLE,DOUBLE,DOUBLE
# )
cmake_minimum_required(VERSION 3.14)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


find_package(QT NAMES Qt6 Qt5 COMPONENTS Core Quick Widgets LinguistTools OpenGL OpenGLExtensions REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core Quick Widgets LinguistTools OpenGL OpenGLExtensions REQUIRED)
message(        ${Qt5OpenGL_INCLUDE_DIRS}
)
if(${QT_VERSION_MAJOR} EQUAL 5)
    set(QTVIDEOSINK_NAME qt5videosink)
    set(QTGLVIDEOSINK_NAME qt5glvideosink)
    set(QWIDGETVIDEOSINK_NAME qwidget5videosink)
    
    include_directories(
        ${Qt5Core_INCLUDE_DIRS}    
        ${Qt5Widgets_INCLUDE_DIRS}
        ${Qt5OpenGL_INCLUDE_DIRS}
        ${Qt5Quick_INCLUDE_DIRS}
        ${Qt5OpenGLExtensions_INCLUDE_DIRS}
    )

elseif(${QT_VERSION_MAJOR} EQUAL 6)
    set(QTVIDEOSINK_NAME qt6videosink)
    set(QTGLVIDEOSINK_NAME qt6glvideosink)
    set(QWIDGETVIDEOSINK_NAME qwidget6videosink)
    include_directories(
        ${Qt6Core_INCLUDE_DIRS}    
        ${Qt6Widgets_INCLUDE_DIRS}
        ${Qt6OpenGL_INCLUDE_DIRS}
        ${Qt6Quick_INCLUDE_DIRS}
        ${Qt6OpenGLExtensions_INCLUDE_DIRS}

        )

endif()


set(GstQtVideoSink_SRCS
    utils/utils.cpp
    utils/bufferformat.cpp

    painters/genericsurfacepainter.cpp

    delegates/basedelegate.cpp
    delegates/qtvideosinkdelegate.cpp
    delegates/qwidgetvideosinkdelegate.cpp

    gstqtvideosinkplugin.cpp
    gstqtvideosinkbase.cpp
    gstqtvideosink.cpp
    gstqwidgetvideosink.cpp

    gstqtvideosinkmarshal.c
)


if (${QT_FOUND})
    set(GstQtVideoSink_SRCS
        ${GstQtVideoSink_SRCS}
        painters/videomaterial.cpp
        painters/videonode.cpp

        delegates/qtquick2videosinkdelegate.cpp

        gstqtquick2videosink.cpp
    )
    set(GstQtVideoSink_LINK_OPENGL TRUE)
endif()

if (${OpenGL_FOUND})
    set(GstQtVideoSink_SRCS
        ${GstQtVideoSink_SRCS}
        painters/openglsurfacepainter.cpp
        gstqtglvideosinkbase.cpp
        gstqtglvideosink.cpp
    )
    set(GstQtVideoSink_test_GL_SRCS
        painters/openglsurfacepainter.cpp
    )
    set(GstQtVideoSink_LINK_OPENGL TRUE)
else()
    add_definitions(-DGST_QT_VIDEO_SINK_NO_OPENGL)
endif()

add_definitions(
    -DQTVIDEOSINK_NAME=${QTVIDEOSINK_NAME}
    -DQTGLVIDEOSINK_NAME=${QTGLVIDEOSINK_NAME}
    -DQWIDGETVIDEOSINK_NAME=${QWIDGETVIDEOSINK_NAME}
)

if (GstQtVideoSink_LINK_OPENGL)
    # if (OPENGLES2_FOUND)
    #     set(GstQtVideoSink_GL_LIBS ${OPENGLES2_LIBRARY})
    #     include_directories(${OPENGLES2_INCLUDE_DIR})
    # else()
        set(GstQtVideoSink_GL_LIBS ${OpenGL_LIBRARIES} )
        include_directories(${OpenGL_INCLUDE_DIRS})
        link_directories(${OpenGL_LIBRARY_DIRS})
        
    # endif()
endif()


link_directories(
    ${GOBJECT_LIBRARY_DIRS}
    ${GSTREAMER_LIBRARY_DIRS}
    ${GSTREAMER_BASE_LIBRARY_DIRS}
    ${GSTREAMER_VIDEO_LIBRARY_DIRS}
    ${Qt5Widgets_LIBRARY_DIRS}
)
add_library(gst${QTVIDEOSINK_NAME} MODULE ${GstQtVideoSink_SRCS})

message(${GSTREAMER_LIBRARIES}
${GSTREAMER_LIBRARY_DIRS}
${OpenGL_LIBRARIES}

)

target_link_libraries(gst${QTVIDEOSINK_NAME} PRIVATE
    ${GOBJECT_LIBRARIES}
    ${GSTREAMER_LIBRARIES}
    ${GSTREAMER_BASE_LIBRARIES}
    ${GSTREAMER_VIDEO_LIBRARIES}
    Qt${QT_VERSION_MAJOR}::Core 
    Qt${QT_VERSION_MAJOR}::Quick
    Qt${QT_VERSION_MAJOR}::Widgets
    Qt${QT_VERSION_MAJOR}::OpenGL
    Qt${QT_VERSION_MAJOR}::OpenGLExtensions
    ${OpenGL_LIBRARIES}
    OpenGL32.Lib
    # D:/Qt/5.15.2/msvc2019_64/lib/libGLESv2.lib

    # D:/Qt/5.15.2/msvc2019_64/lib/libEGL.lib
)
# qt4or5_use_modules(gst${QTVIDEOSINK_NAME} Core Gui Widgets)
# if (Qt4or5_Quick2_FOUND AND (OPENGL_FOUND OR OPENGLES2_FOUND))
#     qt4or5_use_modules(gst${QTVIDEOSINK_NAME} Quick2)
# endif()
# if (Qt4or5_OpenGL_FOUND AND (OPENGL_FOUND OR OPENGLES2_FOUND))
#     qt4or5_use_modules(gst${QTVIDEOSINK_NAME} OpenGL)
# endif()
install(TARGETS gst${QTVIDEOSINK_NAME} DESTINATION ${GSTREAMER_LIBRARY_DIRS}/gstreamer-1.0)

# if (QTGSTREAMER_TESTS)
#     add_executable(qtvideosink_autotest
#         autotest.cpp
#         utils/utils.cpp
#         utils/bufferformat.cpp
#         painters/genericsurfacepainter.cpp
#         ${GstQtVideoSink_test_GL_SRCS}
#     )
#     target_link_libraries(qtvideosink_autotest
#         ${GOBJECT_LIBRARIES}
#         ${GSTREAMER_LIBRARY}
#         ${GSTREAMER_BASE_LIBRARY}
#         ${GSTREAMER_VIDEO_LIBRARY}
#         ${GstQtVideoSink_GL_LIBS}
#     )
#     qt4or5_use_modules(qtvideosink_autotest Core Gui Widgets Test)
#     if (Qt4or5_OpenGL_FOUND AND (OPENGL_FOUND OR OPENGLES2_FOUND))
#         qt4or5_use_modules(qtvideosink_autotest OpenGL)
#     endif()
# endif()
