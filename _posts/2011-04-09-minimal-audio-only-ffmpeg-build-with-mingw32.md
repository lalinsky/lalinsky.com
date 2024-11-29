---
comments: true
date: 2011-04-09 17:22:16
layout: post
slug: minimal-audio-only-ffmpeg-build-with-mingw32
title: Minimal audio-only FFmpeg build with MinGW32
wordpress_id: 1322
categories:
- programming
tags:
- compile
- cross-compile
- ffmpeg
- mingw
- snippet
- windows
---

Another post intended mainly for future me, I tend to lose and re-invent this kind of stuff if it's not public... :)

For projects like [Picard](http://musicbrainz.org/doc/Picard) or [Chromaprint](http://wiki.acoustid.org/wiki/Chromaprint), when I'm doing a Windows build, I need a minimal version of FFmpeg that can decode audio files, but not much else. FFmpeg is a huge project, I definitely don't want to distribute all the video codecs that it supports (e.g. `avcodec-52.dll` from [HawkEye's FFmpeg Windows Builds](http://hawkeye.arrozcru.org/) has 11MB, mine has 1MB). Here is a simple guide how to get a working version using MinGW32 on a Ubuntu machine:


    
    
    sudo apt-get install mingw32 yasm
    mkdir -p ~/code/mingw32/install/
    cd ~/code/mingw32/
    git clone git://git.videolan.org/ffmpeg.git
    cd ffmpeg
    wget -O- http://dl.dropbox.com/u/5215054/win32/ffmpeg/ffmpeg-configure-mingw32.diff | patch -p1
    wget http://dl.dropbox.com/u/5215054/win32/ffmpeg/ffmpeg-configure-mingw32.sh
    chmod +x ffmpeg-configure-mingw32.sh
    PREFIX=~/code/mingw32/install/ ./ffmpeg-configure-mingw32.sh
    make
    make install
    



Ta-da, in `~/code/mingw32/install/` you now have everything you need to compile applications that use FFmpeg with MinGW32. If you need import libraries for MSVC, go to the `bin` directory on Windows and run the following commands:


    
    
    lib /machine:x86 /def:avcodec-52.def /out:..\lib\avcodec.lib
    lib /machine:x86 /def:avformat-52.def /out:..\lib\avformat.lib
    lib /machine:x86 /def:avutil-50.def /out:..\lib\avutil.lib
    



Next up, how to compile Chromaprint on Windows with MSVC 2010 using these libraries.
