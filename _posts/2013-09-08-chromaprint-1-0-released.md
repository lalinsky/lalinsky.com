---
layout: post
title: Chromaprint 1.0 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. There are no changes to the library, just the `fpcalc` utility. The main changes are support for the latest version of the FFmpeg.

The official binaries have been rebuilt with FFmpeg 2.0.1, with added support for reading audio from more video formats. If they are not able to read audio from some format that you use, please let me know.

Download:

 * [Source code tarball](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-1.0.tar.gz) (528.7 KB)
 * Static binaries for the `fpcalc` tool
     * [Windows](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-win32.zip) (1005.6 KB)
     * [Mac OS X, 32-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-osx-i386.tar.gz) (963.2 KB)
     * [Mac OS X, 64-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-osx-x86_64.tar.gz) (954.7 KB)
     * [Linux, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-linux-i686.tar.gz) (1.1 MB)
     * [Linux, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-linux-x86_64.tar.gz) (1.1 MB)

Changes since version 1.0:

 * Support for the latest FFmpeg API.
 * Support for reading audio from stdin in fpcalc.
 * Changed fpcalc to return non-zero status on failure.

