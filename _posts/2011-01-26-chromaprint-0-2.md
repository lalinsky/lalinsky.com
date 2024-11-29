---
comments: true
date: 2011-01-26 19:02:00
layout: post
slug: chromaprint-0-2
title: Chromaprint 0.2
wordpress_id: 1191
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

I'm not good at releasing code, as this should have been done a long time ago, but I've finally released the version 0.2 of Chromaprint. The main changes are new functions in the public API for working with raw fingerprints and safe releasing of memory allocated internally in Chromaprint.

Download:

  * [Source code tarball](http://launchpad.net/chromaprint/trunk/0.2/+download/chromaprint-0.2.tar.gz) (520K)
  * [Ubuntu packages](https://launchpad.net/~luks/+archive/acoustid)

Changes since version 0.1:

  * New public functions `chromaprint_{encode,decode}_fingerprint` for encoding/decoding raw fingerprints.
  * New public function `chromaprint_dealloc` that should be used for releasing all memory allocated in Chromaprint functions.
  * Extended _fpcollect_ to allow processing files with MBIDs.

