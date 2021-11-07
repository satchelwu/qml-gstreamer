cmake_minimum_required(VERSION 3.14)
project("qml-gstreamer")
find_package(QT NAMES Qt6 Qt5 COMPONENTS Core Quick Widgets LinguistTools OpenGL OpenGLExtensions REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core Quick Widgets LinguistTools OpenGL OpenGLExtensions REQUIRED)
find_package(OpenGL REQUIRED)
message(${Qt5Widgets_PRIVATE_INCLUDE_DIRS})


if(NOT ${OpenGL_FOUND})
    message(WARNING "opengl not found")
endif()
if (NOT QT_FOUND)
    message(FATAL_ERROR "QT is required")
endif()

set(QT_VALID ${QT_VERSION_MAJOR} GREATER_EQUAL 5)

if (NOT QT_VALID)
    message(FATAL_ERROR "Qt version < 5 not supported")
endif()
if(QT_FOUND AND QT_VALID)
    add_subdirectory(elements)  
endif()


