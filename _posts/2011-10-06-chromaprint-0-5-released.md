---
comments: true
date: 2011-10-06 12:31:51
layout: post
slug: chromaprint-0-5-released
title: Chromaprint 0.5 released
wordpress_id: 1629
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. The code library hasn't been changed, but there are some bug fixes in the fpcalc utility and the Python bindings.

Download:

  * [Source code tarball](https://github.com/downloads/lalinsky/chromaprint/chromaprint-0.5.tar.gz) (530K)
  * [Ubuntu packages](https://launchpad.net/~luks/+archive/acoustid)
  * Static binaries for the `fpcalc` tool:
    * [Windows, x86](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.5-win32.zip) (634K)
    * [Mac OS X, x86, 10.4+](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.5-osx.tar.gz) (524K)
    * [Linux, x86](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.5-linux.tar.gz) (651K)

Changes since version 0.4:

  * Unicode command line handling in fpcalc.
  * Fixed a crash in fpcalc when FFmpeg was not able to identify the codec.
  * Added encode_fingerprint to the Python bindings.

