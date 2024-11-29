---
layout: post
title: New binaries for Chromaprint 1.0
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

The `fpcalc` binaries included in the [Chromaprint 1.0](http://oxygene.sk/2013/09/chromaprint-1-0-released/)
release had a number of problems. They didn't work on 64-bit Windows and they did not include support for
reading FLAC files.

I've created new builds, which add back FLAC support and the Windows binaries are now created with [mingw-w64](http://mingw-w64.sourceforge.net/),
so we have both 32-bit and 64-bit versions (the 32-bit one should work on 64-bit Windows as well).

Download:

 * [Windows, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-win-i686.zip) (914.7 KB)
 * [Windows, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-win-x86_64.zip) (935.6 KB)
 * [Mac OS X, 32-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-osx-i386.tar.gz) (878.9 KB)
 * [Mac OS X, 64-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-osx-x86_64.tar.gz) (870.7 KB)
 * [Linux, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-linux-i686.tar.gz) (985.8 KB)
 * [Linux, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.0-1-linux-x86_64.tar.gz) (995.1 KB)

