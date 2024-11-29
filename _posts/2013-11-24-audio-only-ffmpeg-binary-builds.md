---
layout: post
title: Audio-only FFmpeg binary builds
categories:
- programming
tags:
- ffmpeg
- programming
---

A side-effect of the new build system for Chromaprint releases is that now I have clean builds of FFmpeg available.
Some people have asked me about this in the past, so I thought I'd make them publicly available.

There are 32-bit and 64-bit binaries for Windows, Linux and OS X.
The packages include all the libraries and include files.
You should be able to use them with any compiler, but I use MSVC on Windows and GCC on Linux and OS X. 
No utilities are included, the packages are meant only for development.

I'll provide no support for the binaries. If you want to use them, you are on your own. You can download them [here](http://build.oxygene.sk/view/FFmpeg/).

