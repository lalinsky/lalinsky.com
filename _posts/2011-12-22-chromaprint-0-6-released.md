---
comments: true
date: 2011-12-22 15:27:50
layout: post
slug: chromaprint-0-6-released
title: Chromaprint 0.6 released
wordpress_id: 1712
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. As with the previous version, the core library hasn't changed much. The fpcalc utility now supports 24-bit sample formats and by default calculates fingerprints using the first 2 minutes of audio. The Python bindings previously included in Chromaprint have been moved to a separate library, [pyacoustid](http://pypi.python.org/pypi/pyacoustid).

Download:

* [Source code tarball](https://github.com/downloads/lalinsky/chromaprint/chromaprint-0.6.tar.gz) (530K)
* Static binaries for the `fpcalc` tool:
  * [Windows](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.6-win32.zip) (695K)
  * [Mac OS X, 32-bit, 10.4+](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.6-osx-i386.tar.gz) (582K)
  * [Mac OS X, 64-bit, 10.4+](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.6-osx-x86_64.tar.gz) (582K)
  * [Linux, 32-bit](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.6-linux-i686.tar.gz) (711K)
  * [Linux, 64-bit](https://github.com/downloads/lalinsky/chromaprint/chromaprint-fpcalc-0.6-linux-x86_64.tar.gz) (689K)

Changes since version 0.5:

* Support for 24-bit file formats in fpcalc.
* The fpcalc utility now uses 120 seconds of audio data by default.
* Python bindings moved to a separate project (pyacoustid).



