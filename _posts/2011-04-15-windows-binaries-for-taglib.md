---
comments: true
date: 2011-04-15 19:07:32
layout: post
slug: windows-binaries-for-taglib
title: Windows binaries for TagLib ...
wordpress_id: 1343
categories:
- taglib
tags:
- cmake
- compile
- msvc
- taglib
- windows
- zlib
---

... or why there are none.

TagLib is a C++ library and as such, it's very dependant on the compiler. GCC (MinGW) and MSVC generate completely different symbol names, so you can't even think about using libraries built the other one. But even if you use different versions of MSVC, you will most likely get into troubles. Different versions of the standard C++ library, different compiler switches and many other things can cause the library to be incompatible with your application.

So, the best solution is to compile TagLib yourself, with the same compiler that you use to build your application. People usually have problems building TagLib with MSVC, so here is a simple guide how to do that.

First, install [CMake](http://www.cmake.org/). Then download [zlib 1.2.5](http://zlib.net/zlib125.zip) and [TagLib 1.7](http://developer.kde.org/~wheeler/files/src/taglib-1.7.tar.gz), unpack both packages somewhere (let's assume you unpacked it to `X:\code`). You can then build TagLib with everything included using the following commands:


    
    
    cd X:\code\zlib-1.2.5
    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=OFF .
    nmake
    cd ..\taglib-1.7
    cmake -DZLIB_INCLUDE_DIR=X:\code\zlib-1.2.5\ -DZLIB_LIBRARY=X:\code\zlib-1.2.5\zlib.lib -DCMAKE_BUILD_TYPE=Release -DWITH_ASF=ON -DWITH_MP4=ON -DBUILD_EXAMPLES=ON .
    nmake
    



Now you can verify that it worked by trying to read a MP3 file (or any format that TagLib supports):


    
    
    copy taglib\tag.dll examples\
    examples\tagreader.exe X:\path\to\music\file.mp3
    



If any of this doesn't work for you, please [let me know](https://mail.kde.org/mailman/listinfo/taglib-devel). You shouldn't need any hacks, the commands described above should work. If they don't, it's a bug and will be fixed.

