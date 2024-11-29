---
comments: true
date: 2010-09-15 07:24:09
layout: post
slug: cross-compiling-with-cmake-and-autotools
title: Cross-compiling with CMake and Autotools
wordpress_id: 657
categories:
- programming
- tools
tags:
- autotools
- cmake
- cross-compile
- mingw
- snippet
- windows
---

[The last time]/2010/07/introducing-chromaprint/) I needed to build a Windows binary, I found it easier to cross-compile it from Linux than to setup a development environment on a freshly installed Windows machine. The problem is that even though it's not hard, you need to remember some obscure options. Today I needed to build a [new version of ISRCsubmit](http://forums.musicbrainz.org/viewtopic.php?pid=11111#p11111) and I had to search for the recipe again. So, let's document it somewhere I can find it in the future...

First, you need the `mingw32` package, which contains the compiler toolkit plus all the required WinAPI libraries. For cross-compiling CMake projects, you also need a file with paths to the MinGW toolkit:

    set(CMAKE_SYSTEM_NAME Windows)
    set(CMAKE_C_COMPILER i586-mingw32msvc-gcc)
    set(CMAKE_CXX_COMPILER i586-mingw32msvc-g++)
    set(CMAKE_FIND_ROOT_PATH /usr/i586-mingw32msvc /home/lukas/projects/win32build)
    set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
    set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
    set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

I have a special directory for installing compiled binaries, which makes it easier to use previously compiled libraries, so that's included in the file. To compile a Windows version of [libmusicbrainz](http://musicbrainz.org/doc/libmusicbrainz), which uses CMake, you can use a serie of command like this:
    
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_TOOLCHAIN_FILE=/home/lukas/projects/mingw.cmake -DCMAKE_INSTALL_PREFIX=/home/lukas/projects/win32build/
    make 
    make install

To build libmusicbrainz I also needed [neon](http://www.webdav.org/neon/), which uses autotools. Fortunately, cross-compiling autotools projects is usually also quite simple, but neon doesn't support MinGW out of the box, so [one patch](https://build.opensuse.org/package/view_file?file=neon-0.29.1-mingw.patch&package=mingw32-libneon&project=windows:mingw:win32&srcmd5=eb762d7b7894e4ebd1cda4f69a6b5c60) is necessary. After applying the patch, it can be compiled like this:
    
    ./configure --host=i586-mingw32msvc --disable-debug --disable-webdav --prefix=/home/lukas/projects/win32build/
    make
    make install
    



