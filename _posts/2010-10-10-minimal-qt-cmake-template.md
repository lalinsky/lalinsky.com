---
comments: true
date: 2010-10-10 12:31:31
layout: post
slug: minimal-qt-cmake-template
title: Minimal Qt/CMake template
wordpress_id: 684
categories:
- programming
tags:
- cmake
- qt
---

I'm starting working on the GUI submission tool for Acoustid and I wanted to use CMake instead of qmake for building the application. I couldn't find anywhere a simple example of what do I have to put into my `CMakeLists.txt` in order to build a Qt application while correctly handling all moc files, ui files and resources.

Here is a minimal template that can be used for starting a Qt/CMake project from scratch:


    
    
    cmake_minimum_required(VERSION 2.6)
    
    project(myproject)
    
    find_package(Qt4 COMPONENTS QtCore QtGui QtNetwork REQUIRED)
    include(${QT_USE_FILE})
    
    set(myproject_HEADERS someclass.h)
    set(myproject_SOURCES someclass.cpp main.cpp)
    set(myproject_UIS myproject.ui)
    set(myproject_RESOURCES myproject.qrc)
    
    qt4_wrap_cpp(myproject_MOC ${myproject_HEADERS})
    qt4_wrap_ui(myproject_UIS_H ${myproject_UIS})
    qt4_add_resources(myproject_RESOURCES_CPP ${myproject_RESOURCES})
    
    include_directories(${CMAKE_CURRENT_BINARY_DIR})
    
    add_executable(myproject
        ${myproject_SOURCES}
        ${myproject_MOC}
        ${myproject_UIS_H}
        ${myproject_RESOURCES_CPP}
    )
    
    target_link_libraries(myproject ${QT_LIBRARIES})
    
