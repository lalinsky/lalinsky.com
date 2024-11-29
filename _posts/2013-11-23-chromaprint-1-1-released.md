---
layout: post
title: Chromaprint 1.1 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. There are a few bug fixes, fixed compilation error on OS X 10.9 and added support for compilation with libav (FFmpeg is still the preferred library to use with Chromaprint).

The official binaries have been build with FFmpeg 2.1. I have switched to a more automated build system for the binaries, so there might be new problems with them. If you find they do not work as expected, please let me know.

Download:

 * [Source code tarball](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-1.1.tar.gz) (529.6 KB)
 * Static binaries for the `fpcalc` tool
   * [Windows, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-win-i686.zip) (972.3 KB)
   * [Windows, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-win-x86_64.zip) (996.4 KB)
   * [Mac OS X, 32-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-osx-i386.tar.gz) (901.2 KB)
   * [Mac OS X, 64-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-osx-x86_64.tar.gz) (891.1 KB)
   * [Linux, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-linux-i686.tar.gz) (1011.4 KB)
   * [Linux, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.1-linux-x86_64.tar.gz) (998.9 KB)

Changes since version 1.0:

 - Fixed potential DoS attack in fingerprint decompression code. (#6)
 - Fixed invalid memory read with some fingerprinter configurations. (#5)
 - Fixed compilation with clang on OS X 10.9 (#7, #9)
 - Added support for audio format conversion with libav. (#2, Gordon Pettey)

