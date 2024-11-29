---
layout: post
title: Chromaprint 1.3 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. This is another small release, there are no changes to the core functionality.

Changes since version 1.2:

- The binary packages have been built with FFmpeg 2.8.6, adding support for DSF files
- You can use use `fpcalc -length 0` to get the full fingerprint
- New function `chromaprint_get_fingerprint_hash` for calculating SimHash from the fingerprint data
- Added info section to the fpcalc executable on Mac OS X
- Generate .pc (pkg-config) file on Mac OS X when not building a framework
- Removed use of some long deprecated FFmpeg APIs
- Some smaller bug fixes

Download:

* [Source code tarball](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-1.3.tar.gz) (525 KB)
* Static binaries for the `fpcalc` tool
  * [Windows, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-win-i686.zip) (1 MB)
  * [Windows, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-win-x86_64.zip) (1 MB)
  * [Mac OS X, 32-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-osx-i386.tar.gz) (964 KB)
  * [Mac OS X, 64-bit, 10.4+](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-osx-x86_64.tar.gz) (944 KB)
  * [Linux, 32-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-linux-i686.tar.gz) (1 MB)
  * [Linux, 64-bit](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.3-linux-x86_64.tar.gz) (1 MB)
