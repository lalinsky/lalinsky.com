---
comments: true
date: 2011-05-14 18:58:46
layout: post
slug: chromaprint-0-4-released
title: Chromaprint 0.4 released
wordpress_id: 1459
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. The main change is addition of an example application that uses the public C API and FFmpeg to allow externals programs to use Chromaprint without linking to the library. Other than this, there were some build system changes and added support for the latest development version of FFmpeg.

Download:

  * [Source code tarball](https://github.com/downloads/lalinsky/chromaprint/chromaprint-0.4.tar.gz) (529K)
  * [Ubuntu packages](https://launchpad.net/~luks/+archive/acoustid)
  * Static binaries for the `fpcalc` tool:
    * [Windows, x86](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.4-win32.zip) (633K)
    * [Mac OS X, x86, 10.4+](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.4-osx.tar.gz) (523K)
    * [Linux, x86](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.4-linux.tar.gz) (650K)

Changes since version 0.3:

  * Support for building a Mac OS X framework.
  * Support for building a static library.
  * Simple C example (fpcalc) that can be used from external applications for fingerprint calculations.

