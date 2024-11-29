---
layout: post
title: Chromaprint 0.7 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. The main change is compatibility with the latest FFmpeg API. There is also a modified fingerprint algorithm, that ignores leading silence up to a specific threshold, but this has no impact on Acoustid users.

Functionality of the `fpcalc` utility has not changed, so I'm not building the binaries for this release. You can use the binaries from version 0.6.

Download:

* [Source code tarball](https://github.com/downloads/lalinsky/chromaprint/chromaprint-0.7.tar.gz) (530K)

Changes since version 0.6:

* Support for the latest FFmpeg API.
* New (non-default) fingerprint algorithm that removes leading silence.
* API to configure fingerprint algorithms, currently only used by `CHROMAPRINT_ALGORITHM_TEST4`.

