---
layout: post
title: Chromaprint 1.2 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. This release doesn't add
any new features to the library, but there are some source code changes
for better compatibility when building the library in non-standard
situations.

Because the functionality hasn't changed, this a source code only release.
You can use fpcalc binaries from version 1.1.

Download:

* [Source code tarball](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-1.2.tar.gz) (517.2 KB)

Changes since version 1.1:

* Fixed compilation with neither libswresample nor libavresample (#11)
* Fixed compilation with static libav (#10)
* Functions chromaprint_encode_fingerprint and chromaprint_decode_fingerprint
   are changed to accept const pointer as input
* Added support for using the Kiss FFT library (should make Android port easier)
* Removed obsolete dev tools from the package
* More compatible DEBUG() macro
